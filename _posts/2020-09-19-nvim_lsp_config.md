---
layout: post
title:  "Customizing Neovim LSP (nvim-lsp)"
date:   2020-09-19 22:51:31 +0530
categories: jekyll update
---

In this post I am gonna discuss customizing neovim LSP according to your needs.
If you haven't setup nvim-lsp till now, you can watch my introductory setup video
for nvim-lsp at: [nvim-lsp setup](https://youtu.be/CCUFml9yB64)

The whole post can be viewed as video at: [youtu.be/9j1Y9CVLvuc](https://youtu.be/9j1Y9CVLvuc)

The post goes with 3 topics:
- Basic keybinds for neovim LSP
- Playing with LSP client configuration
- Extending the capability

For any code structure, you directory structure would look like:

- init.vim
- lua/
	- lsp_config.lua

## Setup basic keybinds

**vim.lsp.buf** contains almost everything what you want.

Go ahead and ask vim for help with ``:h lsp``. Jump to vim.lsp.buf section.
vim.lsp.buf contains these lua functions to help you setup convinient
environment for working.

{% highlight lua %}
vim.lsp.buf.clear_references()
vim.lsp.buf.code_action()
vim.lsp.buf.completion()
vim.lsp.buf.declaration()
vim.lsp.buf.definition()
vim.lsp.buf.document_highlight()
vim.lsp.buf.document_symbol()
vim.lsp.buf.formatting()
vim.lsp.buf.hover()
vim.lsp.buf.implementation()
vim.lsp.buf.incoming_calls()
vim.lsp.buf.outgoing_calls()
vim.lsp.buf.references()
vim.lsp.buf.rename()
vim.lsp.buf.signature_help()
vim.lsp.buf.type_definition()
vim.lsp.buf.workspace_symbol()
{% endhighlight %}

You can define these mappings in lsp_config.lua file itself. For making it
convenient, you can define a map function in your lsp_config.lua file.


{% highlight lua %}
local map = function(type, key, value)
	vim.fn.nvim_buf_set_keymap(0,type,key,value,{noremap = true, silent = true});
end
{% endhighlight %}

Having map functions in lua file itself enables you to activate these keybinds
only if LSP client is attached, otherwise these keybinds would not work. These
is pretty intutive behaviour. And the best thing is you don't need any
autocommand for this behaviour.

This behavious can be easily acheived by mapping these functions in attach
function itself.
So, go ahead and define a custom attach functions as follows:
{% highlight lua %}
local custom_attach = function(client)
	print("LSP started.");
	require'completion'.on_attach(client)
	require'diagnostic'.on_attach(client)

	map('n','gD','<cmd>lua vim.lsp.buf.declaration()<CR>')
	map('n','gd','<cmd>lua vim.lsp.buf.definition()<CR>')
	map('n','K','<cmd>lua vim.lsp.buf.hover()<CR>')
	map('n','gr','<cmd>lua vim.lsp.buf.references()<CR>')
	map('n','gs','<cmd>lua vim.lsp.buf.signature_help()<CR>')
	map('n','gi','<cmd>lua vim.lsp.buf.implementation()<CR>')
	map('n','gt','<cmd>lua vim.lsp.buf.type_definition()<CR>')
	map('n','<leader>gw','<cmd>lua vim.lsp.buf.document_symbol()<CR>')
	map('n','<leader>gW','<cmd>lua vim.lsp.buf.workspace_symbol()<CR>')
	map('n','<leader>ah','<cmd>lua vim.lsp.buf.hover()<CR>')
	map('n','<leader>af','<cmd>lua vim.lsp.buf.code_action()<CR>')
	map('n','<leader>ee','<cmd>lua vim.lsp.util.show_line_diagnostics()<CR>')
	map('n','<leader>ar','<cmd>lua vim.lsp.buf.rename()<CR>')
	map('n','<leader>=', '<cmd>lua vim.lsp.buf.formatting()<CR>')
	map('n','<leader>ai','<cmd>lua vim.lsp.buf.incoming_calls()<CR>')
	map('n','<leader>ao','<cmd>lua vim.lsp.buf.outgoing_calls()<CR>')
end
{% endhighlight %}

First line of this function prints a message when neovim attaches to LSP
server. This is helpful for many slow LSP server like jdtls.

Second line attaches completion-nvim to client.

Third line attaches diagnostic-nvim to client. diagnostic-nvim is a great plugin
for customizing nvim-lsp diagnostics.

Then we define keymaps. Keymaps are self-explainatory with their names.
See ``:help lsp`` for more explanation.

At last just provide this function for on_attach callback to your lsp client
configuration. For example, with tsserver:

{% highlight lua %}
lsp.tsserver.setup{on_attach=custom_attach}
{% endhighlight %}

## Some LSP client configurations

In the video I have explained how to install a language server. However, some
language servers doesn't have :LspInstall commands associated with it
nvim-lspconfig plugin.
This makes people think that those language servers would be hard to install.
However, that is not the case. Actually those language server's binary is
easily available in many software repositories and can be easily installed.

I would explain with the clangd LSP server.
I use Arch (...btw).
In arch, clangd is shipped with clang package itself. So, you can easily install
it using:

	sudo pacman -S clang

Then in lsp_config.lua file you can just add:

{% highlight lua %}
lsp.clangd.setup{on_attach=custom_attach}
{% endhighlight %}

So, anytime you try to install a language server using :LspInstall and you get
an error message, then this is the perfect time to install it from your
distribution's software repository.

As a side note, language servers installed with :LspInstall are stored inside
``$HOME/.cache/nvim/nvim_lsp`` directory.

Coming to lsp.\<lsp-client-name\>.setup. This is a lua table. It contains following
parameters as keys (only important keys as user point of view is mentioned) :
- root_dir
	function of form: func(filename, bufnr). Return root-dir for on which
	lsp server can operate
- filetypes
	set of filetypes to filter for consideration by {root_dir}
- settings
	map with keys corresponding to `workspace/configuration` event responces.
	Different for different LSP server.
- on_attach
	function(client_id) executed with current buffer, when a new LSP server
	attaches.

You can play with root_dir especially to get highly customized behaviour.
settings key is also very powerful that provides you a chance to react on
language specific events. However it is highly undocumented, and commenting
on this currently is not good.

However, I can provide some sample and useful configuration for lua language
server that demonstrates some of it.

To install lua language server do: `:LspInstall sumneko_lua`

Add these lines to end of lsp_config.lua file:

{% highlight lua %}
lsp.sumneko_lua.setup{
	on_attach=custom_attach,
	settings = {
		Lua = {
			runtime = { version = "LuaJIT", path = vim.split(package.path, ';'), },
			completion = { keywordSnippet = "Disable", },
			diagnostics = { enable = true, globals = {
				"vim", "describe", "it", "before_each", "after_each" },
			},
			workspace = {
				library = {
					[vim.fn.expand("$VIMRUNTIME/lua")] = true,
					[vim.fn.expand("$VIMRUNTIME/lua/vim/lsp")] = true,
				}
			}
		}
	}
}
{% endhighlight %}

These lines provide you autocompletion for neovim api while writing lua plugins.


## Extending the capabilities

The best thing about neovim LSP is, with being highly lightweight and
customizable, it is highly extensible too.
If you don't like any default behaviour (which has high probability), unlike
other LSP clients (they force to adopt the behaviour), you can easily change it.
This makes neovim lsp so extensible and customizable on a different level.
What you only need is to learn some lua. And believe me it's easy. It only took
me 20 mins to get familiar with basic syntax and I started to write plugins.

However, this is a very **broad** topic, and needs a **15-20** blog posts like this.
I would focus on extending a specific functionality. And specifically, not
extending it, I would just provide a starting point from where you can dive in.

So, the functionality we are gonna extend (virtually) is lsp-callbacks.
lsp-callbacks are functions called after executing lsp-buffer events (usually)
. Some of those events are listed in keybinding section of blog.

Just let's get back to keybind:
{% highlight lua %}
map('n','<leader>af','<cmd>lua vim.lsp.buf.code_action()<CR>')
{% endhighlight %}

After applying this keybind, you can press \<leader\>af on any symbol with error,
and it would show you possible actions you can do at that instance.
However, it gives list in command line mode and you have to enter a number
to choose the action. This is default behaviour of code action shipped with neovim.
However, I just hate this action (and many of you too hate this for sure).

Luckily this type of action can be easily replaced using lsp-callbacks.
Let's define a simple callback function in lsp_config.lua file:

{% highlight lua %}
local function custom_codeAction_callback(_, _, action)
	print(vim.inspect(action))
end
{% endhighlight %}

Add the following line at end of lsp_config.lua file:

{% highlight lua %}
vim.lsp.callbacks['textDocument/codeAction'] = custom_codeAction_callback
{% endhighlight %}

Now reload any document with LSP attached, you would notice that now on pressing
\<leader\>af it doesn't correct the code or give you any input prompt to enter
choice.

Go ahead and type `:messages` in command line mode of neovim.
You would see a large amount of json code written.
This is what we printed in `custom_codeAction_callback` function.
Based on this text different types of actions can be performed.
You can take a look at `vim.lsp.util` section of `:help lsp` page.
This contains many functions helpful while writing such an extension.

This surely is very simple example and doesn't give you insights on how to handle
actions. However, this certainly gives you a start point to look into a certain
direction.

Personally I extended these beahviour and made it something like this:

#### For code_action:
![codeAction](/images/fix_import.gif)

#### For references:
![codeAction](/images/references.gif)

##### And also for other similar actions.....

If you are interested to know about these extensions, please comment on
corresponding video post on youtube: [youtube video](https://youtu.be/9j1Y9CVLvuc)
so that I can know about it and do a video and blog on this thing specifically.

## Putting things together

### init.vim file


{% highlight vimscript %}
call plug#begin()
Plug 'neovim/nvim-lspconfig'
Plug 'nvim-lua/completion-nvim'
Plug 'nvim-lua/diagnostic-nvim'
call plug#end()

lua require("lsp_config")
{% endhighlight %}

### lsp_config.lua file
{% highlight lua %}
local map = function(type, key, value)
	vim.fn.nvim_buf_set_keymap(0,type,key,value,{noremap = true, silent = true});
end

local custom_attach = function(client)
	print("LSP started.");
	require'completion'.on_attach(client)
	require'diagnostic'.on_attach(client)

	map('n','gD','<cmd>lua vim.lsp.buf.declaration()<CR>')
	map('n','gd','<cmd>lua vim.lsp.buf.definition()<CR>')
	map('n','K','<cmd>lua vim.lsp.buf.hover()<CR>')
	map('n','gr','<cmd>lua vim.lsp.buf.references()<CR>')
	map('n','gs','<cmd>lua vim.lsp.buf.signature_help()<CR>')
	map('n','gi','<cmd>lua vim.lsp.buf.implementation()<CR>')
	map('n','gt','<cmd>lua vim.lsp.buf.type_definition()<CR>')
	map('n','<leader>gw','<cmd>lua vim.lsp.buf.document_symbol()<CR>')
	map('n','<leader>gW','<cmd>lua vim.lsp.buf.workspace_symbol()<CR>')
	map('n','<leader>ah','<cmd>lua vim.lsp.buf.hover()<CR>')
	map('n','<leader>af','<cmd>lua vim.lsp.buf.code_action()<CR>')
	map('n','<leader>ee','<cmd>lua vim.lsp.util.show_line_diagnostics()<CR>')
	map('n','<leader>ar','<cmd>lua vim.lsp.buf.rename()<CR>')
	map('n','<leader>=', '<cmd>lua vim.lsp.buf.formatting()<CR>')
	map('n','<leader>ai','<cmd>lua vim.lsp.buf.incoming_calls()<CR>')
	map('n','<leader>ao','<cmd>lua vim.lsp.buf.outgoing_calls()<CR>')
end

lsp.tsserver.setup{on_attach=custom_attach}
lsp.clangd.setup{on_attach=custom_attach}
lsp.sumneko_lua.setup{
	on_attach=custom_attach,
	settings = {
		Lua = {
			runtime = { version = "LuaJIT", path = vim.split(package.path, ';'), },
			completion = { keywordSnippet = "Disable", },
			diagnostics = { enable = true, globals = {
				"vim", "describe", "it", "before_each", "after_each" },
			},
			workspace = {
				library = {
					[vim.fn.expand("$VIMRUNTIME/lua")] = true,
					[vim.fn.expand("$VIMRUNTIME/lua/vim/lsp")] = true,
				}
			}
		}
	}
}

-- Uncomment to execute the extension test mentioned above.
-- local function custom_codeAction_callback(_, _, action)
-- 	print(vim.inspect(action))
-- end

-- vim.lsp.callbacks['textDocument/codeAction'] = custom_codeAction_callback
{% endhighlight %}

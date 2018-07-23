<!DOCTYPE html>
<html lang="en-us">
  <head>
    <!-- Meta Viewport-->
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <!-- CSS Stylesheet -->
    <link rel="stylesheet" type="text/css" href="responsive.css">
    <!-- Font Awesome Stylesheet: Social Media (and other) icons: http://fontawesome.io/ -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css">
    <!-- Page Title-->
    <title>Coding Commanders</title>
  </head>
  <body>
    <!--- HEADER------------------------>
    <div class = "topContainer">
      
      <div class = "topMenu">
        <a  href = "https://www.youtube.com/channel/UC-gCUMK_EGUqJ7P9VA7BJmQ" class= "fa fa-youtube"> YouTube</a>
        <a href= "https://www.facebook.com/codingcommanders/" class= "fa fa-facebook"> Facebook</a>
        <a href= "https://www.instagram.com/codingcommanders" class= "fa fa-instagram"> Instagram</a>
        <a href= "https://twitter.com/codingCommander" class= "fa fa-twitter"> Twitter</a>
        <a href= "https://plus.google.com/102944579677628755090" class= "fa fa-google"> Google+</a>
        <a href= "https://github.com/codingCommanders/" class= "fa fa-github"> Github</a>
        <a href = "photoGallery.php" class="fa fa-camera" aria-hidden="true"> Photos</a>
      </div>
      <!-- row-->
      <div class = "row">
        <div class = "col-4">
             <!-- Image-->
             <img class = "topImage" src = "http://i67.tinypic.com/2gu04rr.png">
        </div>
        <div class = "col-6">
          <!-- Heading-->
          <div class = "headline">
            <h1>Page Heading</h1>
            <strong><i>Tagline...</i></strong>
         </div>
       </div>
    </div>
    <br><br>
    </div>
    <!--- MAIN CONTENT------------------------>
    <!-- Row 1-->
    <div class = "row">
      <div  align = "center" class = "bordered">Introduction</div>
  </div>
    <!-- Row 2-->
    <div class = "row">
      <!-- Left side column (3/12 or 33.33% of the page width)-->
      <div class = "col-3">
        (Left-side content here)
      </div>
      <!-- Middle column (6/12 or 50% of the page width)-->
      <div class = "col-6">
        <!-- Form-->
        <form class= "myForm" method= "post" action= "main.php">
          <h2>Form Heading</h2>
          <!-- Text imput field-->
         <label class = "redLabel" for = "firstName">First Name</label><br>
         <input type="text" name="firstName" placeholder="First Name" required><br>
         <label class = "redLabel" for = "lastName">Last Name</label><br>
         <input type="text" name="lastName" placeholder="Last Name"><br>
         <label class = "redLabel" for = "userEmail">Email Address:</label><br>
         <input type="email" name="userEmail" placeholder="youremail@email.com" required><br>
         <br><br>
          <!-- Select-->
          <label class = "redLabel" for = "mySelect">Question or Heading</label><br>
          <select name = "mySelect">
            <option></option>
            <option for = "mySelect" value = "1">Option 1</option>
            <option for = "mySelect" value = "2">Option 2</option>
            <option for = "mySelect" value = "3">Option 3</option>
            <option for = "mySelect" value = "4">Option 4</option>
          </select><br>
          <br><br>
          <!-- Radio buttons-->
          <label class = "redLabel" for = "myRadio">Question or Heading</label><br>
            <input type = "radio" name = "myRadio" value = "1">Option 1<br>
            <input type = "radio" name = "myRadio" value = "2">Option 2<br>
            <input type = "radio" name = "myRadio" value = "3">Option 3<br>
            <input type = "radio" name = "myRadio" value = "4">Over 100<br>
          <br><br>
         <!-- Checkboxes-->
         <label class = "redLabel" for = "myChecks">Question or Heading</label><br>
           <input type = "checkbox" name = "myChecks" value = "1">  Option 1<br>
           <input type = "checkbox" name = "myChecks" value = "2">  Option 2<br>
           <input type = "checkbox" name = "myChecks" value = "3">  Option 3<br>
           <br><br>
            <!-- Submit Button-->
            <input type="submit" value="Submit">
        </form>
      </div>
      <!-- Right side column (3/12 or 33.33% of the page width)-->
      <div class = "col-3">
        (Right-side content here)
      </div>
    </div>
   </body>
 </html>

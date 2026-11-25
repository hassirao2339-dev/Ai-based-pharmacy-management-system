================================================================================
  PharmaCare - AI-Based Pharmacy Management System
  INSTALLATION GUIDE / SETUP INSTRUCTIONS
================================================================================

  Final Year Project
  University of Southern Punjab, Multan

  Submitted by:
      Hassan Younis      -  BSSE023R22P-10
      Danish Kaleem      -  BSSE023R22-2

  Supervisor:
      Majid Khawar

  Technology Stack:
      PHP 8 (native, no framework)  |  MySQL 8  |  Apache (XAMPP)
      HTML5, CSS3, Bootstrap 5, jQuery
      Python 3 + Tesseract OCR  (only for prescription text extraction)


================================================================================
  1. WHAT IS INSIDE THIS SUBMISSION FOLDER
================================================================================

  PharmaCare_FYP/
  |
  +-- 1_Source_Code/
  |     +-- pharmacy/          <-- the website (goes inside htdocs)
  |     +-- pms_storage/       <-- private files (goes OUTSIDE htdocs)
  |
  +-- 2_Database/
  |     +-- pharmacy_db.sql    <-- full database export (structure + data)
  |
  +-- 3_Documents/
  |     +-- PMS_SRS_Final_v6.pdf
  |     +-- PharmaCare_Project_Report.pdf
  |     +-- PharmaCare_Test_Checklist.pdf
  |     +-- PharmaCare_Presentation_Guide.pdf
  |
  +-- README_Install.txt       <-- this file


================================================================================
  2. SOFTWARE YOU NEED BEFORE YOU START
================================================================================

  REQUIRED (the system will not run without these)

    1. XAMPP  -  must include PHP 8.0 or higher
       Download: https://www.apachefriends.org
       (Apache + MySQL + phpMyAdmin all come with it)

  OPTIONAL (only needed for the OCR / prescription-scanning feature)

    2. Python 3.10 or higher
       Download: https://www.python.org/downloads/
       IMPORTANT: while installing, tick the box
                  "Add python.exe to PATH"

    3. Tesseract OCR (Windows build by UB Mannheim)
       Download: https://github.com/UB-Mannheim/tesseract/wiki
       Install to the default location:
       C:\Program Files\Tesseract-OCR

    4. Python libraries - open Command Prompt and run:
           pip install pytesseract pillow

  NOTE: If you skip steps 2, 3 and 4, the ENTIRE system still works.
        Only the "Scan prescription with OCR" button will show a
        friendly message saying OCR is not available. The customer can
        still upload the prescription image and the admin can still
        read it manually.


================================================================================
  3. INSTALLATION - STEP BY STEP
================================================================================

  --------------------------------------------------------------------
  STEP 1:  Install XAMPP and start the servers
  --------------------------------------------------------------------

     a) Install XAMPP (remember the folder you install it into,
        for example C:\xampp).

     b) Open the XAMPP Control Panel.

     c) Click "Start" next to Apache.
        Click "Start" next to MySQL.

     d) Both must turn GREEN. If Apache does not start, another program
        is using port 80 (see the Troubleshooting section at the end).


  --------------------------------------------------------------------
  STEP 2:  Copy the website folder
  --------------------------------------------------------------------

     Copy the folder:      1_Source_Code\pharmacy
     Paste it into:        C:\xampp\htdocs

     After this you should have:
         C:\xampp\htdocs\pharmacy\index.php
         C:\xampp\htdocs\pharmacy\app\
         C:\xampp\htdocs\pharmacy\assets\
         C:\xampp\htdocs\pharmacy\python\
         ... etc


  --------------------------------------------------------------------
  STEP 3:  Copy the private storage folder  (IMPORTANT)
  --------------------------------------------------------------------

     Copy the folder:      1_Source_Code\pms_storage
     Paste it into:        C:\xampp
                           (NOT inside htdocs)

     After this you should have:
         C:\xampp\pms_storage\prescriptions\
         C:\xampp\pms_storage\reports\
         C:\xampp\pms_storage\logs\

     WHY OUTSIDE htdocs?
     Because uploaded prescription images are private medical records.
     Anything inside htdocs can be opened by typing its URL in a
     browser. By keeping this folder outside the web root, the images
     can ONLY be served through a PHP page that first checks the login
     session and the ownership of the prescription.


  --------------------------------------------------------------------
  STEP 4:  Create the database
  --------------------------------------------------------------------

     a) Open your browser and go to:
            http://localhost/phpmyadmin

     b) Click "New" in the left sidebar.

     c) Database name:      pharmacy_db
        Collation:          utf8mb4_general_ci
        Click "Create".


  --------------------------------------------------------------------
  STEP 5:  Import the database
  --------------------------------------------------------------------

     a) In phpMyAdmin, click on "pharmacy_db" in the left sidebar.

     b) Click the "Import" tab at the top.

     c) Click "Choose File" and select:
            2_Database\pharmacy_db.sql

     d) Scroll down and click "Import".

     e) Wait for the green message:
            "Import has been successfully finished"

     f) Check the left sidebar - you should now see these 11 tables:
            audit_log                 order_items
            cart                      order_item_batches
            inventory_batches         prescriptions
            medicines                 reports
            orders                    stock_alerts
            users
        ... plus the view: order_trending_summary


  --------------------------------------------------------------------
  STEP 6:  Edit the configuration file  (.env)
  --------------------------------------------------------------------

     Open this file in Notepad or VS Code:
         C:\xampp\htdocs\pharmacy\.env

     Change ONLY these lines to match this computer:

       STORAGE_PATH="C:/xampp/pms_storage"

       PYTHON_PATH="C:/Users/YOUR_NAME/AppData/Local/Programs/Python/Python312/python.exe"

       TESSERACT_PATH="C:/Program Files/Tesseract-OCR/tesseract.exe"

     RULES FOR THIS FILE:
       * Use FORWARD slashes  /   not backslashes  \
       * Keep the double quotes around every path
       * Comments must start with a semicolon  ;   never with  #
       * Do not leave a space around the = sign

     HOW TO FIND YOUR PYTHON PATH:
       Open Command Prompt and type:     where python
       Copy the path it prints, and change every \ into /

     IF YOU DID NOT INSTALL PYTHON / TESSERACT:
       Leave those two lines exactly as they are. The system will
       simply disable the OCR button.

     ALSO CHECK THIS LINE (it should already be correct):
       BASE_URL="http://localhost/pharmacy"


  --------------------------------------------------------------------
  STEP 7:  Open the system
  --------------------------------------------------------------------

     In your browser, go to:
         http://localhost/pharmacy

     The PharmaCare home page should appear.


================================================================================
  4. LOGIN DETAILS
================================================================================

  ADMIN  (pharmacy owner / manager)
      Email     :  admin@gmail.com
      Password  :  admin12345

  CUSTOMER 1  (demo customer, has order history)
      Email     :  ali@gmail.com
      Password  :  ali12345

  CUSTOMER 2  (second demo customer)
      Email     :  test@gmail.com
      Password  :  test12345

  You can also create a brand new customer account from the
  "Create account" button on the home page.

  SECURITY NOTE:
  Admin accounts CANNOT be created through the public registration
  form. Registration always creates a customer. An admin can only be
  created by an existing admin from the admin panel, or directly in
  the database. This is a deliberate security decision.

  LOCKED OUT?
  After 5 wrong password attempts the account is locked for 15
  minutes. The lock is stored in the users table itself, in the
  columns failed_login_attempts and locked_until. To unlock an
  account immediately, run this in phpMyAdmin (SQL tab):

      UPDATE users
      SET failed_login_attempts = 0,
          locked_until          = NULL
      WHERE email = 'ali@gmail.com';


================================================================================
  5. HOW TO CHECK THAT EVERYTHING WORKS  (QUICK TEST, 5 MINUTES)
================================================================================

  1.  Home page opens with medicines showing            http://localhost/pharmacy
  2.  Click "Medicines" - the catalogue loads
  3.  Type in the search box - results update without page reload
  4.  Log in as the customer (ali@gmail.com / ali12345)
  5.  Add a medicine to the cart - the cart badge number increases
  6.  Go to Cart -> Checkout -> Place order
  7.  An order confirmation page appears with an order number
  8.  Log out, then log in as admin (admin@gmail.com / admin12345)
  9.  Admin Dashboard shows statistics cards
  10. Admin -> Orders -> open the new order -> change status to
      "Confirmed" - the customer will now see the new status
  11. Admin -> Reports -> generate a Sales report -> Download PDF
  12. (Only if Python + Tesseract installed)
      Customer -> Upload prescription -> choose a clear photo of
      printed medicine names -> Scan -> suggested medicines appear

  For the complete testing process, see
  3_Documents\PharmaCare_Test_Checklist.pdf  (78 test cases).


================================================================================
  6. RUNNING IT ON A MOBILE PHONE  (same WiFi network)
================================================================================

  1.  The laptop and the phone must be on the SAME WiFi.

  2.  On the laptop, open Command Prompt and type:
          ipconfig
      Find "IPv4 Address", for example  192.168.1.5

  3.  Edit  C:\xampp\htdocs\pharmacy\.env  and change:
          BASE_URL="http://192.168.1.5/pharmacy"

  4.  Allow Apache through the Windows Firewall when it asks
      (or add an inbound rule for port 80).

  5.  On the phone's browser, open:
          http://192.168.1.5/pharmacy

  6.  AFTER THE DEMO, change BASE_URL back to:
          BASE_URL="http://localhost/pharmacy"

  The interface is fully responsive, so it adapts to the phone screen.


================================================================================
  7. TROUBLESHOOTING
================================================================================

  PROBLEM:  Apache will not start (port 80 is busy)
  CAUSE  :  Skype, IIS or another web server is using port 80.
  FIX    :  XAMPP Panel -> Apache "Config" -> httpd.conf
            Change   Listen 80          to   Listen 8080
            Change   ServerName localhost:80  to  localhost:8080
            Save, restart Apache, and in .env set:
                BASE_URL="http://localhost:8080/pharmacy"
            Now open  http://localhost:8080/pharmacy

  --------------------------------------------------------------------
  PROBLEM:  "Setup error: the .env file was not found"
  CAUSE  :  The .env file is missing, or Windows renamed it.
  FIX    :  Make sure the file is named exactly  .env  (with the dot,
            and no .txt at the end). In File Explorer turn on
            View -> File name extensions to check.

  --------------------------------------------------------------------
  PROBLEM:  "Setup error: the .env file has a mistake"
  CAUSE  :  A comment starts with # instead of ; , or a text value is
            missing its double quotes.
  FIX    :  Open .env and make sure every comment line begins with ;
            and every path is wrapped in "double quotes".

  --------------------------------------------------------------------
  PROBLEM:  Database connection error / "Unknown database pharmacy_db"
  CAUSE  :  MySQL is not running, or the import did not finish.
  FIX    :  Start MySQL in the XAMPP panel, then repeat Steps 4 and 5.

  --------------------------------------------------------------------
  PROBLEM:  Prescription upload says "could not save the file"
  CAUSE  :  STORAGE_PATH in .env does not point to a real folder.
  FIX    :  Confirm that  C:\xampp\pms_storage\prescriptions  exists,
            and that STORAGE_PATH uses forward slashes.

  --------------------------------------------------------------------
  PROBLEM:  OCR button says "OCR is not available"
  CAUSE  :  Python or Tesseract is not installed, or the path in .env
            is wrong.
  FIX    :  Install both (Section 2), then run:
                pip install pytesseract pillow
            Test Tesseract from Command Prompt:
                "C:\Program Files\Tesseract-OCR\tesseract.exe" --version
            Test the script directly:
                python "C:\xampp\htdocs\pharmacy\python\ocr_prescription.py" "C:\path\to\image.png"
            It must print one line of JSON.

  --------------------------------------------------------------------
  PROBLEM:  Page is blank / white screen
  CAUSE  :  A PHP error is being hidden.
  FIX    :  In .env temporarily set:
                APP_ENV="development"
                APP_DEBUG=true
            Reload the page to read the error message, then set them
            back to "production" and false.

  --------------------------------------------------------------------
  PROBLEM:  Images and styling do not load (page looks plain)
  CAUSE  :  BASE_URL does not match the folder name or the port.
  FIX    :  BASE_URL must be the address you actually type in the
            browser, with no trailing slash.


================================================================================
  8. FEATURE SUMMARY
================================================================================

  CUSTOMER SIDE
    - Registration, login, logout, forgot / reset password
    - Browse and search the medicine catalogue (live AJAX search)
    - Filter by category, view medicine details
    - Shopping cart with quantity control and stock checking
    - Checkout with cash on delivery
    - Order history, order tracking, order cancellation
    - Upload a prescription image and scan it with OCR
    - Review OCR suggestions and add the matched medicines to the cart
    - Profile management

  ADMIN SIDE
    - Dashboard with live statistics
    - Medicine management (add / edit / soft delete)
    - Inventory batch management with FIFO stock rotation
    - Order management with a controlled status workflow
    - Customer management (activate / deactivate accounts)
    - Automatic low-stock alerts
    - Expiry tracking with early warnings
    - Trending medicines report
    - Sales / inventory / customer reports with PDF download
    - Audit log of every critical admin action

  SECURITY FEATURES
    - Passwords hashed with password_hash() / bcrypt (cost 12)
    - All database access through PDO prepared statements
    - CSRF tokens on every form that changes data
    - Role-based authorisation on every protected page
    - Login attempt limiting with temporary lockout
    - Session timeout and session-name hardening
    - Uploaded files validated by real MIME type, size and image check
    - Uploaded files given random names and stored outside the web root
    - Prescription images served only after an ownership check
    - Security headers (X-Frame-Options, nosniff, Referrer-Policy)
    - Audit logging of critical admin actions
    - Database transactions with row locking so stock can never go wrong


================================================================================
  End of installation guide.  For the design, database and algorithm
  explanation, please see the project report in 3_Documents.
================================================================================

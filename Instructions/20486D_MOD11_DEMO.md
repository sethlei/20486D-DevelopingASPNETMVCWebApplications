# Module 11: Managing Security

# Lesson 1: Authentication in ASP.NET Core 

### Demonstration: How to use ASP.NET Core Identity

#### Preparation Steps 

1. Ensure that you have cloned the **20486D** directory from GitHub. It contains the code segments for this course's labs and demos. 
**(https://github.com/MicrosoftLearning/20486D-DevelopingASPNETMVCWebApplications/tree/master/Allfiles)**

#### Demonstration Steps

1. In the **File Explorer**, navigate to **Allfiles\Mod11\Democode\01_IdentityExample_begin\IdentityExample**, and then copy the address in the address bar.

2. Go to **Start**, and then type **cmd**.

3. Under **Best match**, right-click **Command Prompt**, and then click **Run as administrator**.

4. In the **User Account Control** dialog box, click **Yes**.

5. In the **Administrator: Command Prompt** window, type the following command, and then press Enter.
  ```cs
       cd {copied folder path}
```

>**Note:** If the **{copied folder path}** is different from the disk drive where the **Command Prompt** is located, then you should type **{disk drive}:** before typing the **cd  {copied folder path}** command.

6. In the **Administrator: Command Prompt** window, type the following command, and then press Enter.
  ```cs
       npm install
```
>**Note:** If warning messages are shown in the **Command Prompt** you can ignore them.

7. Close the window.

8. In the **File Explorer**, navigate to **Allfiles\Mod11\Democode\01_IdentityExample_begin**, and then double-click **IdentityExample.sln**.

9. In the **IdentityExample - Microsoft Visual Studio** window, on the **FILE** menu, click **Save All**.

10. In the **IdentityExample - Microsoft Visual Studio** window, on the **DEBUG** menu, click **Start Without Debugging**.

      >**Note:** Examine the page.

11.  In **Microsoft Edge**, click **Close**.

12. In the **IdentityExample - Microsoft Visual Studio** window, in **Solution Explorer**, click **Startup.cs**.

13. In the **Startup.cs** code window, locate the following code:
  ```cs
      using Microsoft.Extensions.DependencyInjection;
```

14. Ensure that the cursor is at the end of the  **Microsoft.Extensions.DependencyInjection** namespace, press Enter, and then type the following code:
  ```cs
      using IdentityExample.Data;
      using IdentityExample.Middleware;
      using IdentityExample.Models;
```

15. In the **Startup.cs** code window, in the **ConfigureServices** method, place the cursor after the **{** (opening braces) sign, press Enter, type the following code, and then press Enter again.
  ```cs
       services.AddDefaultIdentity<Student>(options =>
       {
           options.Password.RequireDigit = true;
           options.Password.RequiredLength = 7;
           options.Password.RequireUppercase = true;

           options.User.RequireUniqueEmail = true;
       })
        .AddEntityFrameworkStores<StudentContext>();
```
16. In the **Startup.cs** code window, in the **Configure** method, locate the following code:
  ```cs
       app.UseStaticFiles();
```

17. Place the cursor at the end of the located code, press Enter twice, and then type the following code:
  ```cs
       app.UseAuthentication();
```
18. In the **IdentityExample - Microsoft Visual Studio** window, in **Solution Explorer**, expand **Controllers**, and then click **StudentController.cs**.

19. In the **StudentController.cs** code window, select the following code:
  ```cs
       public IActionResult Index()
       {
          return View();
       }
```

20. Replace the selected code with the following code:
  ```cs
       public IActionResult Index()
       {
           if (!this.User.Identity.IsAuthenticated)
           {
               return RedirectToAction("Login", "Account");
           }
           return View();
       }
```

21. In the **IdentityExample - Microsoft Visual Studio** window, in **Solution Explorer**, right-click **Controllers**, point to **Add**, and then click **Controller**.

22. In the **Add Scaffold** dialog box, click **MVC Controller - Empty**, and then click **Add**.

23. In the **Add Empty MVC Controller** dialog box, in the **Controller name** text box, type **AccountController**, and then click **Add**.

24. In the **AccountController.cs** code window, locate the following code:
  ```cs
       using Microsoft.AspNetCore.Mvc;
```
25. Ensure that the cursor is at the end of the **Microsoft.AspNetCore.Mvc** namespace, press Enter, and then type the following code:
  ```cs
       using IdentityExample.Models;
       using IdentityExample.ViewModels;
       using Microsoft.AspNetCore.Identity;
```
26. In the **AccountController.cs** code window, select the following code:
  ```cs
       public IActionResult Index()
       {
          return View();
       }
```

27. Replace the selected code with the following code:
  ```cs
       private SignInManager<Student> _signInManager;
       private UserManager<Student> _userManager;

       public AccountController(SignInManager<Student> signInManager, UserManager<Student> userManager)
       {
           _signInManager = signInManager;
           _userManager = userManager;
       }
```

28. Ensure that the cursor is at the end of the **AccountController** constructor code block, press Enter twice, and then type the following code:
  ```cs
       public IActionResult Login()
       {
           if (this.User.Identity.IsAuthenticated)
           {
               return RedirectToAction("Index", "Student");
           }
           return View();
       }
```

29. In the **AccountController.cs** code window, ensure that the cursor is at the end of the **Login** action code block, press Enter twice, and then type the following code:
  ```cs
       [HttpPost]
       public async Task<IActionResult> Login(LoginViewModel loginModel)
       {
           if (ModelState.IsValid)
           {
               var result = await _signInManager.PasswordSignInAsync(loginModel.UserName, loginModel.Password, loginModel.RememberMe, false);
               if (result.Succeeded)
               {
                   return RedirectToAction("Index", "Student");
               }
           }
           ModelState.AddModelError("", "Faild to Login");
           return View();
       }
```

30. In the **AccountController.cs** code window, ensure that the cursor is at the end of the **Login** action code block, press Enter twice, and then type the following code:
  ```cs
       public async Task<IActionResult> Logout()
       {
           await _signInManager.SignOutAsync();
           return RedirectToAction("Index", "Student");
       }
```

31. In the **AccountController.cs** code window, ensure that the cursor is at the end of the **Logout** action code block, press Enter twice, and then type the following code:
  ```cs
       public IActionResult Register()
       {
           return View();
       }
```

32. In the **AccountController.cs** code window, ensure that the cursor is at the end of the **Register** action code block, press Enter twice, and then type the following code:
  ```cs
       [HttpPost]
       public async Task<IActionResult> Register(RegisterViewModel registerModel)
       {
           if (ModelState.IsValid)
           {
               Student student = new Student
               {
                   FirstName = registerModel.FirstName,
                   LastName = registerModel.LastName,
                   UserName = registerModel.UserName,
                   PhoneNumber = registerModel.PhoneNumber,
                   Email = registerModel.Email
               };

               var result = await _userManager.CreateAsync(student, registerModel.Password);
               if (result.Succeeded)
               {
                   return RedirectToAction("Login", "Account");
               }
               foreach (var error in result.Errors)
               {
                   ModelState.AddModelError("", error.Description);
               }
           }
           return View();
       }
```

33. In the **IdentityExample - Microsoft Visual Studio** window, on the **FILE** menu, click **Save All**.

34. In the **IdentityExample - Microsoft Visual Studio** window, on the **DEBUG** menu, click **Start Without Debugging**.

35. In the menu bar, click **Student Portal**.

      >**Note:** Student Portal page is only for authorized users therefore you redirected to the login page.

36. On **Login**, click **Register**.

37. On **Register**, in the **First Name** text box, type _&lt;A first name of your choice&gt;._

38. On **Register**, in the **Last Name** text box, type _&lt;A last name of your choice&gt;._

39. On **Register**, in the **Phone Number** text box, type _&lt;A phone number of your choice&gt;._

40. On **Register**, in the **Email** text box, type _&lt;An email time of your choice&gt;._

41. On **Register**, in the **User Name** text box, type _&lt;A user name of your choice&gt;._

42. On **Register**, in the **Password** text box, type _&lt;123qwe!@#QWE&gt;,_ and then click **Register**.

43. On **Login**, in the **User Name** text box, type _&lt;The registered user name&gt;._

44. On **Login**, in the **Password** text box, type _&lt;123qwe!@#QWE&gt;,_ and then click **Login**.

      >**Note:** Examine the page, you have been authorized to enter the Student Portal page.

45.  In **Microsoft Edge**, click **Close**.

46. In the **IdentityExample  - Microsoft Visual Studio** window, on the **FILE** menu, click **Exit**.

# Lesson 2: Authorization in ASP.NET Core

### Demonstration: How to Authorize Access to Controller Actions

#### Preparation Steps 

1. Ensure that you have cloned the **20486D** directory from GitHub. It contains the code segments for this course's labs and demos. 
**(https://github.com/MicrosoftLearning/20486D-DevelopingASPNETMVCWebApplications/tree/master/Allfiles)**

#### Demonstration Steps

1. In the **File Explorer**, navigate to **Allfiles\Mod11\Democode\02_AuthorizeExample_begin\IdentityExample**, and then copy the address in the address bar.

2. Go to **Start**, and then type **cmd**.

3. Under **Best match**, right-click **Command Prompt**, and then click **Run as administrator**.

4. In the **User Account Control** dialog box, click **Yes**.

5. In the **Administrator: Command Prompt** window, type the following command, and then press Enter.
  ```cs
       cd {copied folder path}
```

>**Note:** If the **{copied folder path}** is different from the disk drive where the **Command Prompt** is located, then you should type **{disk drive}:** before typing the **cd  {copied folder path}** command.

6. In the **Administrator: Command Prompt** window, type the following command, and then press Enter.
  ```cs
       npm install
```
>**Note:** If warning messages are shown in the **Command Prompt** you can ignore them.

7. Close the window.

8. In the **File Explorer**, navigate to **Allfiles\Mod11\Democode\02_AuthorizeExample_begin**, and then double-click **IdentityExample.sln**.

9. In the **IdentityExample - Microsoft Visual Studio** window, in **Solution Explorer**, click **Startup.cs**.

10. In the **Startup.cs** code window, locate the following code:
  ```cs
      using Microsoft.Extensions.DependencyInjection;
```
11. Ensure that the cursor is at the end of the  **Microsoft.Extensions.DependencyInjection** namespace, press Enter, and then type the following code:
  ```cs
      using Microsoft.AspNetCore.Identity;
```

12. In the **Startup.cs** code window, select the following code:
  ```cs
       services.AddDefaultIdentity<Student>(options =>
```

13. Replace the selected code with the following code:
  ```cs
       services.AddIdentity<Student, IdentityRole>(options =>
```

14. In the **IdentityExample - Microsoft Visual Studio** window, in **Solution Explorer**, expand **Controllers**, and then click **StudentController.cs**.

15. In the **StudentController.cs** code window, locate the following code:
  ```cs
      using Microsoft.AspNetCore.Mvc;
```

16. Ensure that the cursor is at the end of the  **Microsoft.AspNetCore.Mvc** namespace, press Enter, and then type the following code:
  ```cs
      using Microsoft.AspNetCore.Authorization;
      using IdentityExample.Data;
```

17. In the **StudentController.cs** code window, select the following code:
  ```cs
       public IActionResult Index()
       {
           if (!this.User.Identity.IsAuthenticated)
           {
               return RedirectToAction("Login", "Account");
           }
           return View();
       }
```

18. Replace the selected code with the following code:
  ```cs
       private StudentContext _studentContext;

       public StudentController(StudentContext studentContext)
       {
           _studentContext = studentContext;
       }

       [Authorize]
       public IActionResult Index()
       {
           return View();
       }
```

19. In the **StudentController.cs** code window, ensure that the cursor is at the end of the **Index** action code block, press Enter twice, and then type the following code:
  ```cs
       [AllowAnonymous]
       public IActionResult CourseDetails()
       {
           return View(_studentContext.Courses.ToList());
       }
```

20. In the **IdentityExample - Microsoft Visual Studio** window, on the **FILE** menu, click **Save All**.

21. In the **IdentityExample - Microsoft Visual Studio** window, on the **DEBUG** menu, click **Start Without Debugging**.

22. In the menu bar, click **Student Portal**.

      >**Note:** Student Portal page is only for authorized users therefore you redirected to the login page.

23. In the menu bar, click **Our Courses**.

      >**Note:** Our Courses page is authorized for all site visitors therefore you can enter the page without registration.
      
23. In the menu bar, click **Login**.

24. On **Login**, click **Register**.

25. On **Register**, in the **First Name** text box, type _&lt;A first name of your choice&gt;._

26. On **Register**, in the **Last Name** text box, type _&lt;A last name of your choice&gt;._

27. On **Register**, in the **Phone Number** text box, type _&lt;A phone number of your choice&gt;._

28. On **Register**, in the **Email** text box, type _&lt;An email time of your choice&gt;._

29. On **Register**, in the **User Name** text box, type _&lt;A user name of your choice&gt;._

30. On **Register**, in the **Password** text box, type _&lt;123qwe!@#QWE&gt;,_ and then click **Register**.

31. On **Login**, in the **User Name** text box, type _&lt;The registered user name&gt;._

32. On **Login**, in the **Password** text box, type _&lt;123qwe!@#QWE&gt;,_ and then click **Login**.

      >**Note:** Examine the page, you have been authorized to enter the Student Portal page.

33.  In **Microsoft Edge**, click **Close**.

34. In the **IdentityExample  - Microsoft Visual Studio** window, on the **FILE** menu, click **Exit**.

©2018 Microsoft Corporation. All rights reserved.

The text in this document is available under the  [Creative Commons Attribution 3.0 License](https://creativecommons.org/licenses/by/3.0/legalcode), additional terms may apply. All other content contained in this document (including, without limitation, trademarks, logos, images, etc.) are  **not** included within the Creative Commons license grant. This document does not provide you with any legal rights to any intellectual property in any Microsoft product. You may copy and use this document for your internal, reference purposes.

This document is provided &quot;as-is.&quot; Information and views expressed in this document, including URL and other Internet Web site references, may change without notice. You bear the risk of using it. Some examples are for illustration only and are fictitious. No real association is intended or inferred. Microsoft makes no warranties, express or implied, with respect to the information provided here.
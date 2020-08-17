# ChutzpahDebugIssue

## Summary
Debugging with Server mode almost always fails.
It looks like the test harness is completing and the web server is being closed BEFORE VS has time to get in and connect.

## Details

Microsoft Visual Studio Enterprise 2019
Version 16.7.1
VisualStudio.16.Release/16.7.1+30406.217
Microsoft .NET Framework
Version 4.8.03752

Chutzpah 4.4.10
Chutzpah Context Menu   4.4.9
Chutzpah Test Adapter for Visual Studio 2012   4.4.9

IE 11.959.18362.0

Typescript 3.9
Output EcmaScript5

Solution is set to run a single test in PhantomJS (default) but with Web Server Mode turned on.
- Build the solution so that Chutzpah picks up the single test.
- Put a breakpoint in the test.
- Locate the test in test explorer.
 -Run Debug and wait.

Expected: Browser starts. Jasmine page is loaded. Breakpoint is hit.

Actual: Most of the time it will launch an IE window and throw up a "Cannot reach this page" error. 
(It doesn't seem to matter what default browser is set to or what the browser selection for the project is - it always opens IE).
URL is of the expected form: http://localhost:9876/users/jswallow/source/repos/chutzpahtest2/chutzpahtest2/scripts/_Chutzpah.ca2581215930830d6ae529bc6dfd3dc2e2908df8.test.html
Refreshing the page throws the same error but you can observe in the Developer Tools that the error happens BEFORE the call to the url is actually made. 
The call to the URL always returns a 200 response but the response will be one of two cases:
(i) empty
(ii) body contains the jasmine spec runner page as you would expect.
The Jasmine icon is displayed in the browser address bar so the favicon file was retrieved (100% of the time).
Visual Studio continues with the attached debugger until the IE window is closed (as you would expect).

Occasionally it will work as expected and the breakpoint is hit. (I can determine no pattern to when it does this).
It then briefly works but and the URL can even be reached if pasted into Chrome but it quickly stops working and the "Cannot reach this page" pops up.

Tests run using the console.exe and /OpenInBrowser run all the time as expected.

Remove Server from the config to go back to the old file mode of operation makes it work as expected (unsurprisingly as the file hanges around). 
Unfortunately this is not a solution as this won't help if you have newer jscript code than the ES5 level supported by Phantom. Going to Engine "Chrome" or Engine: "JSDom"
turns on Server mode and so this issue comes into play.

Tests executed run as expected. 

Symptomatically it looks like the web browser terminates early. The fact that the body sometimes seems to contain some content suggests that some of the time the system 
gets in soon enough to load the base page and that goes into the browser cache but then bombs out. Later attempts then return this cached information?

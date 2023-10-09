# BrowserSessions

Saves a HTML and CSV file of your Safari and Chrome tabs to a directory.

You may need to `mkdir ~/Desktop/BrowserSessions`

The code below shows what's in the app (you can unzip and then open it with Automator).

```js
function run(input, parameters) {
	
// ==========================================================
	

var content = "";
var line = "";
var this_content = '';
var delimeter = ',';
line = 'browser_name' + delimeter + 'win_num' + delimeter + 'tab_num' + delimeter + 'tab_name' + delimeter + 'tab_url' + "\n";
content = content + line;

var outfile = "browser_pages.csv";
var app = Application.currentApplication();
app.includeStandardAdditions = true;


function alert_to_textEdit(textfile, string) {

	var textEdit = Application("TextEdit");
	var newDoc = textEdit.Document().make();

	newDoc.name = textfile;
	textEdit.documents[textfile].text = string;

}


     
    function writeTextToFile(text, file, overwriteExistingContent) {
	
        try {
     
            // Convert the file to a string
            var fileString = file.toString()
     
            // Open the file for writing
            var openedFile = app.openForAccess(Path(fileString), { writePermission: true })
     
            // Clear the file if content should be overwritten
            if (overwriteExistingContent) {
                app.setEof(openedFile, { to: 0 })
            }
     
            // Write the new content to the file
            app.write(text, { to: openedFile, startingAt: app.getEof(openedFile) })
     
            // Close the file
            app.closeAccess(openedFile)
     
            // Return a boolean indicating that writing was successful
            return true
        }
        catch(error) {
     
            try {
                // Close the file
                app.closeAccess(file)
            }
            catch(error) {
                // Report the error is closing failed
                console.log(`Couldn't close file: ${error}`)
            }
     
            // Return a boolean indicating that writing was successful
            return false
        }
    }



function alert(mesg) {
	var app = Application.currentApplication();
	app.includeStandardAdditions = true;
	app.displayDialog(mesg);
}

function get_browser_pages(browser_name) {
	var my_line = '';
	var my_content = '';
	var my_html_content = '';
	var my_html_line = '';
	var html_cell = `
	<tr>
      <th scope="row">row_num</th>
      <td> browser_name </td>
      <td> win_num </td>
      <td> tab_num </td>
	  <td> <a href='tab_url'>tab_name</a> </td>
    </tr>
	`;

	var this_browser = Application(browser_name);
	var browser_nwin = this_browser.windows.length;

	
	var k = 0;
	for (j = 0; j < browser_nwin; j++) {    

    	var window = this_browser.windows[j];
		var win_num = j;
		var win_ntabs = window.tabs.length;
	

    	for (var i = 0; i < win_ntabs; i++) {
			var tab_name = window.tabs[i].name();
			tab_name = tab_name.replace(',',' ');
			var tab_num = i;
			var tab_url = window.tabs[i].url();
        	my_line = browser_name + delimeter + win_num + delimeter + tab_num + delimeter + tab_name + delimeter + tab_url + "\n";
			my_content += my_line;
			my_html_line = html_cell;
			my_html_line = my_html_line.replace('row_num', k);
			my_html_line = my_html_line.replace('browser_name', browser_name);
			my_html_line = my_html_line.replace('win_num', win_num);
			my_html_line = my_html_line.replace('tab_num', tab_num);
			my_html_line = my_html_line.replace('tab_name', tab_name);
			my_html_line = my_html_line.replace('tab_url', tab_url);
			my_html_content += my_html_line;
    	}
		k += 1;	
   }
   return [my_content, my_html_content];
}

function get_sensible_date(){
	var d = new Date();
	var datestring = '';
	datestring += d.getFullYear();
	datestring += String(d.getMonth()).padStart(2, '0');
	datestring += String(d.getDate()).padStart(2, '0');
	datestring += '_';
	datestring += String(d.getHours()).padStart(2, '0');
	datestring += String(d.getMinutes()).padStart(2, '0');
	datestring += String(d.getSeconds()).padStart(2, '0');
	return datestring;
}


var this_html = '';
var this_content = '';
var html_body = '';

var browser_name = 'Google Chrome';
[this_content, this_html] = get_browser_pages(browser_name);
content += this_content;
html_body += this_html;

var browser_name = 'Safari';
[this_content, this_html] = get_browser_pages(browser_name);
content += this_content;
html_body += this_html;


var dateString = get_sensible_date();
//alert(dateString);

var desktopString = app.pathTo("desktop").toString();
var file = `${desktopString}/BrowserSessions/${dateString}.csv`
writeTextToFile(content, file, true)



var html_head = `

<!doctype html>
<html lang="en">
  <head>
    <!-- Required meta tags -->
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@4.0.0/dist/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">

    <title>Hello, world!</title>
  </head>
  <body>

<table class="table table-striped">
  <thead class="thead-dark">
    <tr>
      <th scope="col">#</th>
      <th scope="col">browser_name</th>
      <th scope="col">win_num</th>
      <th scope="col">tab_num</th>
	  <th scope="col">Webpage</th>
    </tr>
  </thead>
  <tbody>
  `;


var html_foot = `
</tbody>
</table>
  </body>
</html>
`;

var html = html_head + html_body + html_foot;
file = `${desktopString}/BrowserSessions/${dateString}.html`
writeTextToFile(html, file, true)


// ==========================================================

	return input;
}

```

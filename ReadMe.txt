*** Syntax for mail merge templates ***

Put the text in a rich text field named 'Body' on the template document. Use the syntax below to display values from the processed document in the resulting document.
Note that there is functionality in the script library and in the control codes/place holder below that reference documents and structure that does not exist in the sample database.
You can read more on my blog at http://blog.texasswede.com/code-mail-mergeform-letters-in-lotuscript/
---------------------------------------

{fieldname}
Displays the content of the specified field from the selected source document. Additional (optional) formatting arguments can be used, for example to format values to desired format. 
By using the argument LOSSNOTICE or SOURCE="LOSSNOTICE" the value of the field is retrieved from the Loss Notice instead of the current source document, e.g. {adjuster LOSSNOTICE}.
Use the argument SELECTED to get the fields name, address, city, state, zip, email, SSN and DoB for the recipient selected in the separate dialog box.
{email SELECTED} will return the email address for the recipient selected, either producer, insured or one of the claimants.
The optional argument NABFIELD will retrieve the value of the specified field from the NAB for the user specified in the field (field must be spelled exactly as in the NAB design): 
{Adjuster NABFIELD="JobTitle"} will return the title of the person in the 'Adjuster' field.
Any date/time field can be reformated using the FORMAT argument (see %DATE and %TIME for details).
Any numeric field can be reformated using the FORMAT argument with a format string:
FORMAT="#,##0.00"	-> Two decimals, comma for thousand delimiter
FORMAT="0000"		-> Four digits, padded with leading zeros


{%DATE FORMAT="formatstring"}
Displayes the current date. Default format is mm/dd/yyyy but the FORMAT argument can be used to change the value into desired format. 
Examples of date formats:
FORMAT="mmmm d, yyyy"	->  March 3, 2010
FORMAT="yyyy-mm-dd"	->  2010-03-01
FORMAT="mmmm yyyy"	->  March 2010
The date can be adjusted using the ADJUSTYEAR, ADJUSTMONTH and ADJUSTDAY arguments:
ADJUSTDAY="14"		-> Increase the date by 14 days
ADJUSTMONTH="-3"	-> Decrease the date by 3 months

{%TIME}
Displayes the current time. Default format is hh:nn:ss (24h universal format) but like with the date, the FORMAT argument can be used to change the value into desired format. Note that minutes use the letter N, not M (which is used for month)!
Examples of time formats:
FORMAT="h.nn ampm"	->  2.34 pm
FORMAT="hh.nn ampm"	->  02.34 pm
FORMAT="hh:nn"  ->  14:34
The time can be adjusted using the ADJUSTHOUR and ADJUSTMINUTE arguments:
ADJUSTHOUR="6"		-> Increase the time by 6 hours
ADJUSTMINUTE="-45"	-> Decrease the time by 45 minutes


{%INPUT PROMPT="Please enter the amount" FORMAT="$#,##0.00" REQUIRED SETVAR="variablename"}
Asks the user to enter a value. The optional arguemnt REQUIRED is used to force the user to enter a value (blank values will not be accepted). FORMAT can be used to format the value entered, for example into correct currency format (as shown above) or desired date format.
Additionally, SETVAR="variablename" can be used to store the value entered for re-use later in the document, where {%GETVAR NAME="variablename"} is used to retrieve/display it.


{%GETVAR NAME="variablename"}
Get the value previously stored in an {%INPUT} command using the SETVAR argument.


{%USER} or {%USR}
Displayes the current user's name. It will be the name of the user creating the form letter. {%USR} have been deprecated, it is just available for backwards compatibility. The optional argument NABFIELD will retrieve the value of the specified field from the NAB for the current user (field must be spelled exactly as in the NAB design): 
{%USER NABFIELD="JobTitle"}


{%PICKLIST SERVER="servername" DB="database" VIEW="viewname" FIELD="fieldname" PROMPT="text}
This command let the user select a document from a list, and returns the value in the specified field. SERVER and DB are optional, and defaults to current server and current database. VIEW, FIELD and PROMPT are required. Optionally CLEARCACHE can be used for multiple lookups to the same view where different documents are to be selected.
By using the FILTER argument, the document collection will be filtered to only those where the category (in a categorized view) matches the value in the specified field in the source document. E.g: VIEW="(ClaimantList)" FILTER="ParentUNID" will only show documents in the (categorized) lookup view with the same ParentUNID as the document being processed (merged).
By specifying the optional keyword/argument CLAIMANT, the VIEW, PROMPT and FILTER argumenst are set automatically as follows: VIEW="(SysLookupClaimantsCatByParentUNID)" PROMPT="Select Claimant:" FILTER="ParentUNID".


{%PICKLIST VIEW="viewname" FIELD="fieldname" CACHED}
This command is used after the initial picklist command. It will retrieve additional fields from the same (cached) document, so the user only have to select the document once but can retrieve multiple field values from it. If another {%PICKLIST} command is encountered with the CLEARCACHE argument, the cached document will not be available anymore.

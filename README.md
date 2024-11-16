# Hacks
These are all "hacks" that I have spent countless of hours on realizing because of the abundant and often contradictory information found on the internet (stackxchange, etc.), but also erroneous solutions generated by, e.g., ChatGPT.
## Report Pages
### Interactive Reports (IR)
#### Conditional Edit Column
##### Use Case
Current user shall be able to edit report rows based on condition(s).
##### Example
- **Context**: The example is based on an IR sourced from a regular table, the editing takes place via a modal dialog.
- **Edit Condition**: OPEN_ID column contains same user name as current user name.
- **Style**: Edit column with pencil style icon to edit rows where authorized.

The built-in link attribute of a page body doesn't do the job, because with its condition statements, the whole link column is either turned on or off, no can do for individual rows.
That's why it has to be done with a combination of SQL and a helper column that will contain the conditional edit buttons.

###### Report Page Body
The SQL in the **report page** body selects the existing table data and creates the helper column ```EDIT_LINK```. The column is empty where the condition is not met, it contains the URL (via ```apex_page.get_url()```) and the icon (fa-edit) where the condition is met.
```
select ID,
       firstname,
       lastname,
       EMAIL,
       cellphone,
       phone,
       ADDRESS_1_STR,
       ADDRESS_2_ADD,
       ADDRESS_3_PC,
       ADDRESS_4_CITY,
       OPEN_ID,
       CASE lower(OPEN_ID)
         when lower(:APP_USER) THEN
            '<a href="'||apex_page.get_url(p_page => '3', p_items => 'p3_id', p_values => ID)||'"><span class="fa fa-edit"></span></a>'
        ELSE NULL
    END EDIT_LINK
  from SDV_PERSONS
```
###### Helper Column ```EDIT_LINK```
The column text is 
```EDIT_LINK``` Column settings:
- **Link type**: ```Link```
- **Heading**: ```&nbsp;```
- **Link**
  - **Target**: ```Page <x>```
  - **Link Text**: ```&EDIT_LINK.``` (see last source)

##### Sources
- https://forums.oracle.com/ords/apexds/post/how-to-open-modal-dialog-page-from-hyperlink-6555
- https://docs.oracle.com/en/database/oracle/apex/22.1/aeapi/GET_URL-Function.html
- https://forums.oracle.com/ords/apexds/post/how-to-make-link-text-conditional-6681
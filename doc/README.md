# Documentation - tManipulateJSON
# Talend components to filter or edit JSON strings

These Talend components are designed for the easy filtering and editing of JSON strings. More specifically, tFilterJSON filters an input row for JSON strings with given pairs of key/value; tRenameJSON allows JSON keys to be renamed; tTransposeJSON fills a template JSON string with values taken from an input JSON string; tTruncateJSON allows JSON properties to be removed.

<br>
<hr>

## tFilterJSON

*filters an input row for JSON strings with given pairs of key/value*

<br>

### Features

- choose from 11 operations to set up filtering conditions
- specify the date format in the input JSON string
- interpret 0 (false) and 1 (true) as boolean values
- choose separator for decimal numbers found in the input JSON strings (period or comma)

<br>

### External libraries
- [jackson-core-2.13.5.jar](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.13.5)
- [jackson-databind-2.13.5.jar](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.13.5)
- [jackson-annotations-2.13.5.jar](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.13.5)

The .jar are included in the component folder. The components have not been tested with other versions of the [Jackson library](https://github.com/FasterXML/jackson).   

<br>

### Configuration - Basic settings

![Screenshot of tFilterJSON Basic settings](/doc/images/tfilterjson_basic_settings.png)

1. Select the **input JSON column** from the dropdown list.  
 *The data type of the input JSON column must be String, otherwise an exception is thrown upon execution of the job.*

2. Use the **+** and **X** buttons to add or remove a condition, respectively. **IMPORTANT: Multiple conditions are combined using the AND logical operator.**

3. Enter the **Target (XPath)** to the value from the input JSON string that will be evaluated. This is the left side of the comparison.      
 *There is no need to quote this value. If the key does not exist in the input JSON string, the input row is rejected.*

4. Select the **Operation** to perform from the dropdown list. If a type is given, then the value of the key entered in step 3 must be castable to this type in order for the condition to be evaluated.  

5. Enter the **Value** to which the value in the input JSON string is to be compared. This is the right side of the comparison.   
 *There is no need to enter a value if the operation selected in step 4 is Exists, Is null or Is not null.*

<br>

### Configuration - Advanced settings

![Screenshot of tFilterJSON Advanced settings](/doc/images/tfilterjson_advanced_settings.png)

1. Check **Filter out null input JSON string** to reject null input JSON string. If this box is unchecked, null input JSON string are not evaluated.   
 *This option is set by default. Note that a {} input JSON string is not null.*

2. If the input JSON string contains date values which are to be evaluated, specify the **Date format in input JSON string** (default is yyyy-MM-dd HH:mm:ss). See the [Talend documentation](https://help.talend.com/r/en-US/8.0/data-preparation-user-guide/list-of-date-and-date-time-formats) for available date/time formats.   

3. If numbers are to be evaluated (last 3 operations), specify the **decimal separator in input JSON string** (period [default], comma, or both).   

<br>

### Global variable

NB_LINE: the number of rows read by the component.  This is an After variable and it returns an integer.

NB_LINE_OK: the number of rows that had an input JSON string which matched the filtering conditions. This is an After variable and it returns an integer.

NB_LINE_REJECT: the number of rows that had an input JSON string which did not match the filtering conditions, including those which had an invalid input JSON string or for which an error occurred during filtering. This is an After variable and it returns an integer.  

<br>

### Usage

The component is not startable.

<br>

### Example 1 - Filtering an input JSON string for a minimum amount in a credit property

![Screenshot of tFilterJSON Example](/doc/images/tfilterjson_example1a.png)

<br>

1. Drop a tFixedFlowInput with schema ```id``` (integer) and ```json``` (string).
2. Check ```Use Single Table```.
3. Specify the following input row:  
    ```
    id = 1
    json = "{\"account\":{\"credit\":\"1,23\"}, \"name\":\"hermann\"}"
    ```
4. Drop a tFilterJSON and connect to the tFixedFlowInput.  

<br>

![Screenshot of tFilterJSON Example Basic settings](/doc/images/tfilterjson_example1b.png)

<br>

5. Select ```json``` as the input JSON column.
6. Click on the + button and add the following condition: Target (XPath) = /account/credit, Operation = Number - superior to, Value = 1.

<br>

![Screenshot of tFilterJSON Example Advanced settings](/doc/images/tfilterjson_example1c.png)

<br>

7. Select ```Comma``` as the decimal separator in the input JSON string.
8. Drop 2 tLogRow and connect one to the Filter flow of the tFilterJSON and the other to the Reject flow of the tFilterJSON.
9. Run the job.


<br>
<br>
<hr>

## tRenameJSON

*allows JSON keys to be renamed*

<br>

### Features

- reject input row to a secondary flow if the input JSON string is invalid   

<br>

### External libraries
- [jackson-core-2.13.5.jar](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.13.5)
- [jackson-databind-2.13.5.jar](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.13.5)
- [jackson-annotations-2.13.5.jar](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.13.5)

The .jar are included in the tFilterJSON component folder. The components have not been tested with other versions of the [Jackson library](https://github.com/FasterXML/jackson).   

<br>

### Configuration - Basic settings

![Screenshot of tRenameJSON Basic settings](/doc/images/trenamejson_basic_settings.png)

1. A column named **renamedJson** is added to the output schema. This column contains the new JSON string generated by the component.   
 *The output schema is read-only and can therefore not be modified.*

2. Select the **input JSON column** from the dropdown list.  
 *The data type of the input JSON column must be String, otherwise an exception is thrown upon execution of the job.*

3. Use the **+** and **X** buttons to add or remove a key to rename, respectively.

4. Enter the **Target (XPath)** to the key to rename.      
 *There is no need to quote this value. If the key does not exist in the input JSON string, nothing occurs.*

5. Enter the **New name** of the key.

6. Select one of 3 ways to proceed if an error occurs during treatment of the input row:
   - redirect the input row to a **reject flow** (default)
   - **assign a null value** to the renamedJson column
   - **die** (i.e. throw an exception). This will interrupt the data flow.   

 *The most likely cause of error is an invalid input JSON string.*

<br>

### Global variable

NB_LINE: the number of rows read by the component.  This is an After variable and it returns an integer.

NB_LINE_OK: the number of rows that had a valid input JSON string which could be processed. This is an After variable and it returns an integer.

NB_LINE_ERROR: the number of rows that had an invalid input JSON string or for which an error occurred during processing. This is an After variable and it returns an integer.  

<br>

### Usage

The component is not startable.

<br>
<br>
<hr>

## tTransposeJSON

*fills a template JSON string with values taken from an input JSON string*

<br>

### Features

- reject input row to a secondary flow if the input JSON string is invalid   

<br>

### External libraries
- [jackson-core-2.13.5.jar](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.13.5)
- [jackson-databind-2.13.5.jar](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.13.5)
- [jackson-annotations-2.13.5.jar](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.13.5)

The .jar are included in the tFilterJSON component folder. The components have not been tested with other versions of the [Jackson library](https://github.com/FasterXML/jackson).   

<br>

### Configuration - Basic settings

![Screenshot of tTransposeJSON Basic settings](/doc/images/ttransposejson_basic_settings.png)

1. A column named **transposedJson** is added to the output schema. This column contains the new JSON string generated by the component.   
 *The output schema is read-only and can therefore not be modified.*

2. Select the **input JSON column** from the dropdown list.  
 *The data type of the input JSON column must be String, otherwise an exception is thrown upon execution of the job.*

3. Enter the **Target (template) JSON** string. Values should be given for every key. If the key does not exist in the input JSON string, then the value entered in the template will be used as default.  
 *This template cannot be an array. Unescaped double quotes, carriage returns and tabs can be used. The component will format the entry as needed.*

4. Use the **+** and **X** buttons to add or remove a mapping between the new JSON string and the input one, respectively.

5. Enter the **Target XPath** of the key in the target JSON target from step 3.      
 *If the key does not exist in the target JSON, an exception is thrown upon execution of the job.*

6. Enter the **Source XPath** to the value in the input JSON. This value will be assigned to the target key from step 5. If the key does not exist in the input JSON string, the value in the new JSON string is the one specified in the target (template) JSON.

7. Select one of 3 ways to proceed if an error occurs during treatment of the input row:
   - redirect the input row to a **reject flow** (default)
   - **assign a null value** to the transposedJson column
   - **die** (i.e. throw an exception). This will interrupt the data flow.   

 *The most likely causes of error is an invalid input JSON string.*

<br>

### Global variable

NB_LINE: the number of rows read by the component.  This is an After variable and it returns an integer.

NB_LINE_OK: the number of rows that had a valid input JSON string which could be processed. This is an After variable and it returns an integer.

NB_LINE_ERROR: the number of rows that had an invalid input JSON string or for which an error occurred during processing. This is an After variable and it returns an integer.  

<br>

### Usage

The component is not startable.

<br>
<br>
<hr>

## tTruncateJSON

*allows JSON properties to be removed*

<br>

### Features

- reject input row to a secondary flow if the input JSON string is invalid   

<br>

### External libraries
- [jackson-core-2.13.5.jar](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.13.5)
- [jackson-databind-2.13.5.jar](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.13.5)
- [jackson-annotations-2.13.5.jar](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.13.5)

The .jar are included in the tFilterJSON component folder. The components have not been tested with other versions of the [Jackson library](https://github.com/FasterXML/jackson).   

<br>

### Configuration - Basic settings

![Screenshot of tTruncateJSON Basic settings](/doc/images/ttruncatejson_basic_settings.png)

1. A column named **truncatedJson** is added to the output schema. This column contains the new JSON string generated by the component.   
 *The output schema is read-only and can therefore not be modified.*

2. Select the **input JSON column** from the dropdown list.  
 *The data type of the input JSON column must be String, otherwise an exception is thrown upon execution of the job.*

3. Use the **+** and **X** buttons to add or remove a key to remove, respectively.

4. Enter the XPath to the key to remove.      
 *There is no need to quote this value. If the key does not exist in the input JSON string, nothing occurs.*

5. Select one of 3 ways to proceed if an error occurs during treatment of the input row:
   - redirect the input row to a **reject flow** (default)
   - **assign a null value** to the truncatedJson column
   - **die** (i.e. throw an exception). This will interrupt the data flow.   

 *The most likely causes of error is an invalid input JSON string.*


<br>

### Configuration - Advanced settings

![Screenshot of tTruncateJSON Advanced settings](/doc/images/ttruncatejson_advanced_settings.png)

1. Check **Convert empty JSON string to null** to assign null to the truncatedJson output column in the event that all properties have been removed from the input JSON string, i.e. {} = null.   

<br>

### Global variable

NB_LINE: the number of rows read by the component.  This is an After variable and it returns an integer.

NB_LINE_OK: the number of rows that had a valid input JSON string which could be processed. This is an After variable and it returns an integer.

NB_LINE_ERROR: the number of rows that had an invalid input JSON string or for which an error occurred during processing. This is an After variable and it returns an integer.  


<br>

### Usage

The component is not startable.

<br>

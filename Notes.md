# Notes
Created to help me understand the frameworks provided
<br />
- The ApplicationTemplate has a PropertyManager, an ErrorDialog, and ConfirmationDialog
- When start method is called, the ErrorDialog and ConfirmationDialog instances are initiated on the primaryStage
- Before proceeding, it checks the propertyManger if null (look below) or if error occurred when loading the properties from properties.xml, if not, the userInterface is audited.
- If error occured while loading data, errorDialog is shown
- ApplicationTemplate then creates a new UI component by calling the constructor of UITemplate (window, height, title are instantiated by the properties from the propertymanager)
- The UITemplate does the following:
    - The correct paths to all the required resources are set (resources such as newiconPath, saveiconPath, etc.)
    - Initialize the top toolbar
    - Set the toolbar button handlers
        - INITIIALIZATION IS NOT PROVIDED AT THE TEMPLATE-LEVEL, AND MUST BE IMPLEMENTED BY A CHILD CLASS
    - Start the app window (without the application-specific workspace)
- Will display an error dialog because it is a template
___

## xmlutil Module
### xmlutil
__XMLUtilities__ <br />
_Provides methods for interaction with XML data_
1. __validateXML(__ _URL xmlFilePath, URL xmlSchemapath_ __)__
    - Returns true if the xml file is valid according the the schema, else false
2. __loadXMLDocument(__ _URL xmlFilePath, URL schemaFileURL_ __)__
    - Returns org.w3c.com.Document object with data from the original xml file
3. __getNodeWithName(__ _Document doc, String tagName_ __)__
    - Returns (1st) Node in the Document specified by tag name, else null
4. __getTextData(__ _Document doc, String tagName_ __)__
    - Returns String representation of Node in the Document specified by tag name, else null
5. __getChildrenWithName(__ _Node parent, String tagName_ __)__
    - Returns all children of the specified parent node with the specified tag name,

__InvalidXMLFileFormatException__
    - Exception class that occurs when file is not valid according to the defined schema

## vlij Module

### components
__ActionComponent__ [Interface] <br />
_Defines (minimal) behavior of core actions_
1. void handleNewRequest()
2. void handleSaveRequest()
3. void handleLoadRequest()
4. void handleExitRequest()
5. void handlePrintRequest()

__ConfirmationDialog__ (extends --> Stage, implements --> Dialog) <br />
_Provides template for displaying 3 way confirmation messages_
_Essentially this class is like a stage that set allows you to customize the title and the message_
_PRIVATE CONSTRUCTOR_ -basically can only create instance in the ConfirmationDialog class
1. enum Option{ YES, NO, CANCEL }
2. __(static) getDialog()__
    - Returns dialog --> this is also where the instance is created
3. __init(__ _Stage owner_ __)__
    - Completely initializes the dialog to be used, owner = window on top of which dialog will be displayed
4. __show(__ _String dialogTitle, String message_ __)__
    - Loads the specified title and message into the dialog and then displays the dialog
5. __getSelectedOption()__
    - Returns the Option selected

__DataComponent__ [Interface] <br />
_Defines (minimal) methods for data management_
1. void loadData(Path dataFilePath)
2. void saveData(Path dataFilePath)
3. clear()

__Dialog__ [Interface] <br />
_Defines (minimal) behavior of pop up dialogs_
1. enum DialogType{ ERROR, CONFIRMATION }
2. void show(String title, String message)
3. void init(Stage owner)

__ErrorDialog__ <br />
_Provides the template for displaying error messages, only has close button_
_Essentially this class is another stage that allows you to customize the title and the message_
_PRIVATE CONSTRUCTOR_ -basically can only create instance in the ErrorDialog class
1. __init(__ _Stage owner_ __)__
    - Completely initializes the error dialog
2. __show(__ _String errorDialogTitle, String errorMessage_ __)__
    - Loads the specified title and message into the dialog and then displays the dialog

__UIComponent__ [Interface] <br />
_Defines (minimal) functionality of graphical user interface of a ViliJ application_
1. Stage getPrimaryWindow()
2. Scene getPrimaryScene()
3. String getTitle()
4. void initialize()
5. void clear()

### propertymanager
__PropertyManager__ <br />
_Core class that defines all the global properties to be used by the Vilij framework_
_PRIVATE CONSTRUCTOR_ -basically can only create instance in the PropertyManager class
1. Constants required to load the elements and their properties from the XML properties file
    - PROPERTY_ELEMENT = "property"
    - PROPERTY\_LIST\_ELEMENT = "property_list"
    - PROPERTY\_OPTIONS\_LIST\_ELEMENT = "property\_options\_list"
    - PROPERTY\_OPTIONS\_ELEMENT = "property_options"
    - OPTION_ELEMENT = "option"
    - NAME_ATTRIBUTE = "name"
    - VALUE_ATTRIBUTE = "value"
2. Path of the properties resource folder, relative to the root resource folder for the application
    - PROPERTIES\_RESOURCE\_RELATIVE\_PATH = "properties"
3. __(static) getManager() __
    - Returns PropertyManager --> this is where the instance is also created (can be null if the initialization xml is not validated to the initialization schema)
    - This also loads calls the loadProperties method
    - Creates HashMap of properties and another one of propertyOptions
4. __addProperty(__ _String property, String value_ __)__
    - Adds the property to PropertyManager's HashMap
5. __getPropertyValue(__ _String property_ __)__
    - Returns property value as a String, based on the string property name
6. __getPropertyValuesAsInt(__ _String property_ __)__
    - Returns property value as an integer, based on the string property name --> can throw Exceptions (NullPointer, NumberFormat)
7. __getPropertyValueAsBoolean(__ _String property_ __)__
    - Returns true if property value is "true" (ignoring case) based on the string property name
8. __addPropertyOption(__ _String property, String option_ __)__
    - Throws exception if property does not exist (NoSuchElement)
    - Add property option to specified string property name
9. __getPropertyOptions(__ _String propterty_ __)__
    - Throws exception if property does not exist (NoSUchElement)
    - Returns a list of property options (strings)
10. __hasProperty(__ _Object property_ __)__
    - Returns true if property exists (Notice that the parameter is an Object!)
11. __loadProperties(__ _Class klass, String xmlfilename, String schemafilename_ __)__
    - Parameters are strings of the file names
    - Document created so that a propertyListNode is retrieved such that its children are the properties.
    - For each property from the XML, NamedNodeMap (kinda like a list of attributes of the property) gets the name and value pairs and puts them in the properties HashMap of the PropertyManager
    - propertyOptionsListNode retrieved, if exists, a list of the property options are retrieved
    - For each node in the property options list, the name of the property is retrieved
    - For each option of the property, the option is added to the array list of the property in the property Options HashMap
    - Basically, this method loads all the properties and the property options for each property in the Property Manager's hashmap

### settings
__PropertyTypes__ <br />
_This enumerable type, lists the various high-level property types listed in the initial set of properties to be loaded from the global properties xml file specified by the initialization parameters_
1. It is an enum of PropertyTypes, view file for specific property types. Property types include:
    - high-level user interface properties
    - resource files and folders
    - user interface icon file names
    - tooltips for user interface buttons
    - error titles (these reflect the type of error encountered)
    - error messages for errors that require an argument
    - standard labels and titles

__InitializationParams__ <br />
_This is the set of parameters specified for the proper initialization of a Vilij application_
_Two error-specific parameters are included to handle the case when the property file(s) cannot be loaded (which are the other parameters)_
1. enum InitializationParams (Each InitializationParams has a getParameterName() method, returns string)
    - LOAD\_ERROR\_TITLE("Load Error")
    - PROPERTIES\_LOAD\_ERROR\_MESSAGE("An error occured while loading the property file")
    - PROPERTIES\_XML("properties.xml")
    - WORKSPACE\_PROPERTIES\_XML("app-properties.xml")
    - SCHEMA\_DEFINITION("property-schema.xsd")

### templates
__ApplicationTemplate__ (extends --> Application) <br />
_This class is the minimal template for a Vilij application. It does not create an actual workspace within the window and does not perform any actions_
_LOOK AT NOTES ABOVE_
_UIComponent initialized by constructor of UITemplate
1. __getDataComponent()__
    - Returns DataComponent
2. __getUIComponent()__
    - Returns UIComponent
3. __getActionComponent()__
    - Returns ActionComponent
4. __setDataComponent(__ _DataComponent component_ __)__
    - Set DataComponent
5. __setUIComponent(__ _UIComponent component_ __)__
    - Set UIComponent
6. __setActionComponent(__ _ActionComponent component_ __)__
    - Set Action Component
7. __getDialog(__ _Dialog.DialogType dialogType_ __)__
    - Return dialog based on argument

__UITemplate__ (implements --> UIComponent) <br />
_This class defines and creates a user interface using the ApplicationTemplate class. The interface created here at the framework-level does not instantiate an actual workspace (which must be done by any applicatiion using this framework)_

1. __UITemplate(__ _Stage primaryStage, ApplicationTemplate applicationTemplate_ __)__ (Constructor)
    - Creates the minimal user interface. It uses the window height and width properties and creates a toolbar with the required buttons. (More details are above) Methods called are:
        - setResourcePaths(applicationTemplate)
        - setToolBar(applicationTemplate)
        - setToolbarHandlers(applicationTemplate)
        - setWindow(applicationTemplate)
2. Methods from UIComponent are overriden (__getPrimaryWindow()__, __getPrimaryScene()__, __getTitle()__, and __initialize()__)
    - initialize method designed to throw an exception because it is a template
3. __setToolBar(__ _ApplicationTemplate applicationTemplate_ __)__
    - Initialize the top toolbar by calling __setToolBar(...)__ method for each button on the toolbar
4. __setToolBarButton(__ _String iconPath, String tooptip, boolean disabled_ __)__
    - Returns and initializes button based on arguments
5. __setResourcePaths(__ _ApplicationTemplate applicationTemplate_ __)__
    - Sets the correct paths to all the required resources, which are the class fields/properties
6. __setToolBarHandlers(__ _ApplicationTemplate applicationTemplate_ __)__
    - implementation must be done in child class
7. __clear()__
    - implementation must be donte in child class
8. __setWindow(__ _Application applicationTemplate_ __)__
    - starts the app window (without the application-specific workspace)

## data-vlij Module

### actions
__AppActions__ (implements --> ActionComponent) <br />
_This is the concrete implementation of the action handlers required by the application_
    - dataFilePath (package private) Path variable
1. __AppActions(__ _ApplicationTemplate applicationTemplate_ __)__ (Constructor)
    - Sets the application template
2. other methods are parts of the HW

### dataprocessors
__AppData__ (implements --> DataComponent) <br />
_This is the concrete application-specific implementation of the data component defined by the Vilij framework_
1. __AppData(__ _ApplicationTemplate applicationTemplate_ __)__ (Constructor)
    - Sets the application template and instantiates TSDProcessor
2. __displayData()__
    - Calls TSDProcessor to display data
3. other methods are parts of the HW

__TSDProcessor__ (FINAL class) <br />
_The data files used by this data visualization applications follow a tab-separated format, where each data point is named, labeled, and has a specific location in the 2-dimensional X-Y plane._
_This class handles the parsing and processing of such data. It also handles exporting the data to a 2-D point._
__SAMPLE FILE IN FORMAT IS IN resources/data folder__
1. __TSDProcessor()__ (Constructor)
    - Initializes dataLabelss map (String, String) and dataPoints map (String, Point2D)
2. __processString(__ _String tsdString_ __)__
    - Processes the data and populate the two maps with the data, can throw Exception if error occurs
    - dataLabels.put(name, label)
    - dataPoints.put(name, point)
3. __toCharData(__ _XYZChart<Number, Number> chart_ __)__
    - Exports the data to the specified 2-D chart
4. __clear()__
    - Clears the dataPoints and dataLabels hashMaps

### settings
__AppPropertyTypes__ <br />
_This enumerable type lists the various application-specific property types listed in the initial set of properties to be loaded from the workspace properties xml file specified by the initialization parameters_
1. View the file for specific property types. Property types include:
    - resource files and folders
    - user interface icon file names
    - tooltips for user interface buttons
    - error messages
    - application-specific message titles
    - application-specific messages
    - application-specific parameters

### ui
__AppUI__ (extends --> UITemplate) <br />
_The application's user interface implementation_
    - ApplicationTemplate applicationTemplate (package private class member/field)
1. __AppUI(__ _Stage primaryStage, ApplicationTemplate applicationTemplate_ __)__
    - Calls super method and initializes application template
2. __getChart()__
    - Returns chart (ScatterChart<Number, Number>)
3. __setResourcePaths(__ _ApplicationTemplate applicationTemplate_ __)__
4. __setToolbarHandlers(__ _ApplicationTemplate applicationTemplate_ __)__
    - Gives buttons handlers
5. other methods are for hws

__DataVisualizer__ (extends --> ApplicationTemplate) <br />
_The main class for which the application is run. The  various components used here must be concred implementations of types defined in vlij.components_

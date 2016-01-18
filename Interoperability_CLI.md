# PREFORMA suppliers Interoperability Group
## Interoperability Shell- Shell using CLI

### 1. Objective

At the last interoperability meeting, on 21 December 2015, we agreed on creating interoperability between suppliers shells using the Command Line Interface (CLI) for the April workshop.

The aim of this document is to define an starting point to discuss the CLI commands needed in the Shell-Shell communication.

### 2. CLI requirements

-	Ask for a single file check and produce a common XML report.
-	Attach conformance checkers using the shell CLI.
-	Get information about the Conformance Checkers.
-	Configure remote Conformance Checkers.

### 3. CLI Usage

For interoperability purposes, the shells will have two main execution modes.

-	Usage mode 1: For execution. With this mode, a single file (either tiff, pdf or movie) is passed to the application. The program starts a thread for processing it and returns a job id, so that we can make calls with this id to know the state of that job. 

-	Usage mode 2: For configuring the conformance checkers. We can request a list of connected conformance checkers, and adding, deleting and configuring them.

Each call produces an XML output, which is parsed by the shell that made the call, to continue its processing.

### 3.1. Execution mode

#### Syntax

```dpfmanager check [<path> | --state <jobid> ]```

#### Examples

Example 1. Check a file

Run the program to check a file. A job id is returned. Internally, the shell decides which conformance checker is able to process the file (it can be either its own built-in conformance checker, or an external one), and executes it.

```$ dpfmanager check image1.tif```
	
```xml
	<job>
		<id>1001</id>
		<path>image1.tif</path>
		<conformancechecker>Tiff</conformancechecker>
	</job>
```

Example 2. Get the state of a job

Ask for the state of a previously started job. The possible states can be: “Running”, “Finished”, “Waiting” or “Unknown”. If the state is “Running”, the field “progress” may contain the percentage of completion of the task. If the state is “Finished”, then the field “report” contains the path to the xml containing the report.

```$ dpfmanager check --state 1001```

```xml
	<job>
		<id>1001</id>
		<path>image1.tif</path>
		<conformancechecker>Tiff</conformancechecker>
		<state>Finished</state>
		<progress>100</progress>
		<report>c:/reports/1/report.xml</report>
	</job>
```

### 3.2. Configuration mode

#### Syntax

```dpfmanager modules (--list |--add  <path> | --remove <name> |-- edit <name> <path> | --info <name>  | --enable <name> | --disable <name> | configure <name> <configuration>)```

```Options:
  -l --list		Show the list of conformance checkers
  -r --remove	Remove a conformance checker from the list
  -e --edit	Edit the execution path of a conformance checker
  --enable	Activate a conformance checker
  --disable	Deactivate a conformance checker
  --configure	Set the configuration of a conformance checker
  -i --info		Get information of a conformance checker
  --state	Gets the progress state of a job
  -h --help	Shows this screen
```

#### Examples

Example 3. Get the list of available conformance checkers

This call returns an XML with the set of available conformance checkers, with its name, location, state, and selected configuration.

```$ dpfmanager modules --list```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<tns:ListOutput xmlns:tns="http://www.preforma-project/interoperability" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://www.preforma-project/interoperability preformainteroperability.xsd ">
  <ConformanceCheckers>
    <conformanceChecker>
      <name>Tiff</name>
      <location>built-in</location>
      <state>enabled</state>
      <defaultConfiguration>baseline</defaultConfiguration>
    </conformanceChecker>
    <conformanceChecker>
      <name>Pdf</name>
      <location>/usr/local/bin/pdfcc</location>
      <state>enabled</state>
      <defaultConfiguration>config1</defaultConfiguration>
    </conformanceChecker>
    <conformanceChecker>
      <name>Video</name>
      <location>127.0.0.1:8080</location>
      <state>disabled</state>
      <defaultConfiguration>config2</defaultConfiguration>
    </conformanceChecker>
  </ConformanceCheckers>
</tns:ListOutput>
```

Example 4. Get the information of a particular conformance checker

The information of a conformance checker can be known asking by its name. And an XML is returned, containing its file identification settings, implemented standards, and available configurations to use.

```$ dpfmanager modules -i Tiff```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<tns:ConformanceCheckerInfo xmlns:tns="http://www.preforma-project.eu/interoperability"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xsi:schemaLocation="http://www.preforma-project.eu/interoperability preformainteroperability.xsd ">
 <name>Tiff Conformance Checker</name>
 <author>author</author>
 <version>10.00.10</version>
 <company>Easy Innova</company>
 <media_type>Tiff</media_type>
 <extensions>
  <extension>tif</extension>
  <extension>tiff</extension>
 </extensions>
 <magicNumbers>
  <MagicNumber>
   <offset>0</offset>
   <signature>4949</signature>
  </MagicNumber>
  <MagicNumber>
   <offset>0</offset>
   <signature>4D4D</signature>
  </MagicNumber>
 </magicNumbers>
 <implementationChecker>
  <standards>
   <standard>
    <name>Tiff/EP</name>
    <ISO>12234-2:2001</ISO>
    <description>Electronic Still Picture Imaging Removable Memory</description>
   </standard>
   <standard>
    <name>Tiff/IT</name>
    <ISO>12639:2004</ISO>
    <description>Tag image file format for image technology</description>
    <subStandards>
     <standard>
      <name>Tiff/IT-P1</name>
      <ISO>12639:2004</ISO>
      <description>Tiff/It Profile 1</description>
     </standard>
     <standard>
      <name>Tiff/IT-P2</name>
      <ISO>12639:2004</ISO>
      <description>Tiff/It Profile 2</description>
     </standard>
    </subStandards>
   </standard>
  </standards>
 </implementationChecker>
 <configurations>
  <configuration>
   <name>Baseline</name>
   <description>Check Tiff Baseline 6.0 conformance</description>
  </configuration>
 </configurations>
</tns:ConformanceCheckerInfo>
```

Example 5. Change the configuration of a conformance checker

The selected configuration file for each conformance checker can be changed to another one of its available configurations.

```$ dpfmanager modules --configure Tiff JsonPDF```

###4. XML definition

```xml
<?xml version="1.0" encoding="UTF-8"?>
<schema targetNamespace="http://www.preforma-project.eu/interoperability" xmlns="http://www.w3.org/2001/XMLSchema" xmlns:tns="http://www.preforma-project.eu/interoperability" xmlns:Q1="output">

    <element name="ListOutput" type="tns:ListOutputTypeList"></element>

    <complexType name="ListOutputType">
    	<sequence minOccurs="1" maxOccurs="unbounded">
    		<element name="name" type="string"
    			minOccurs="1" maxOccurs="1">
    		</element>
    		<element name="location" type="string"></element>
    		<element name="state" minOccurs="1" maxOccurs="1">
    			<simpleType>
    				<restriction base="string">
    					<enumeration value="enabled"></enumeration>
    					<enumeration value="disabled"></enumeration>
    				</restriction>
    			</simpleType>
    		</element>
            <element name="defaultConfiguration" type="string"></element>
        </sequence>
    </complexType>

    <element name="ConformanceCheckerInfo" type="tns:ConformanceCheckerType"></element>


    <complexType name="Shell">
    	<sequence>
    		<element name="Name" type="string"></element>
    		<element name="Author" type="string"></element>
    		<element name="Version" type="string"></element>
    		<element name="Company" type="string"></element>
    		<element name="URL" type="string"></element>
    	</sequence>
    </complexType>

    <complexType name="ConformanceCheckerType">
    	<sequence>
    		<element name="name" type="string" minOccurs="1" maxOccurs="1"></element>
    		<element name="author" type="string" minOccurs="1" maxOccurs="1"></element>
    		<element name="version" minOccurs="1" maxOccurs="1">
    			<simpleType>
    				<restriction base="string">
    					<pattern value="[0-9]*.[0-9]*.[0-9]*"></pattern>
    				</restriction>
    			</simpleType>
    		</element>
    		<element name="company" type="string" minOccurs="1" maxOccurs="1"></element>
    		<element name="media_type" type="string" minOccurs="1" maxOccurs="1"></element>
    		<element name="extensions"
    			type="tns:ExtensionList" minOccurs="1" maxOccurs="1">
    		</element>
    		<element name="magicNumbers"
    			type="tns:MagicNumberList" minOccurs="1" maxOccurs="1">
    		</element>
    		<element name="implementationChecker"
    			type="tns:ImplementationCheckerType" minOccurs="1" maxOccurs="1">
    		</element>
    		<element name="configurations" type="tns:configurationsList" minOccurs="1" maxOccurs="1"></element>
    	</sequence>
    </complexType>


    <complexType name="ExtensionList">
    	<sequence>
    		<element name="extension" type="string" maxOccurs="unbounded" minOccurs="1"></element>
    	</sequence>
    </complexType>

    <complexType name="MagicNumberList">
    	<sequence>
    		<element name="MagicNumber" type="tns:MagicNumberType" minOccurs="1" maxOccurs="unbounded"></element>
    	</sequence>
    </complexType>

    <complexType name="MagicNumberType">
    	<sequence minOccurs="1">
    		<element name="offset" type="int" minOccurs="1" maxOccurs="1"></element>
    		<element name="signature" type="hexBinary" minOccurs="1" maxOccurs="1"></element>
    	</sequence>
    </complexType>

    <complexType name="ImplementationCheckerType">
    	<sequence>
    		<element name="standards" type="tns:StandardsList"></element>
    	</sequence>
    </complexType>

    <complexType name="standardsType">
    	<sequence>
    		<element name="name" type="string" minOccurs="1" maxOccurs="1"></element>
    		<element name="ISO" type="string" minOccurs="1" maxOccurs="1"></element>
    		<element name="description" type="string" minOccurs="1" maxOccurs="1"></element>
    		<element name="subStandards" type="tns:StandardsList" minOccurs="0" maxOccurs="1"></element>
    	</sequence>
    </complexType>

    <complexType name="StandardsList">
    	<sequence>
    		<element name="standard" type="tns:standardsType" minOccurs="1" maxOccurs="unbounded"></element>
    	</sequence>
    </complexType>

    <complexType name="configurationsType">
    	<sequence>
    		<element name="name" type="string"></element>
    		<element name="description" type="string"></element>
    	</sequence>
    </complexType>

    <complexType name="configurationsList">
    	<sequence>
    		<element name="configuration" type="tns:configurationsType"></element>
    	</sequence>
    </complexType>

    <complexType name="ListOutputList">
    	<sequence>
    		<element name="conformanceChecker" type="tns:ListOutputType" minOccurs="1" maxOccurs="unbounded"></element>
    	</sequence>
    </complexType>

    <complexType name="ListOutputTypeList">
    	<sequence>
    		<element name="ConformanceCheckers" type="tns:ListOutputList" minOccurs="1" maxOccurs="1"></element>
    	</sequence>
    </complexType>

    <element name="JobState" type="tns:JobStateType"></element>
    
    <complexType name="JobStateType">
    	<sequence>
    		<element name="Status" type="string"></element>
    	</sequence>
    </complexType>
</schema>
```

### 5. Error codes (to be extended…):

- 1001: No conformance checker found for this file
- 1002: Conformance checker not available
- 2001: Configuration not found

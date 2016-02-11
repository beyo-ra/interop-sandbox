PREFORMA Interoperability Framework
===================================

Abbreviations
=============
|     | Explaination
| :-- | :---
| CC  | Conformance Checker
| CLI | Command Line Interface
| PEA | PREFORMA Extension API
| PIA | PREFORMA Intergration API
| PIF | PREFORMA Interoperability 

Introduction
============

Overview
--------

The PIF consist of two set of APIs: the PIA and PEA.

The PIA defines a method to integrate an external system with a CC. Discussed in Section 1.

The PEA defines the method to extend a CC (Conformance Checker) to check for other types of resources not supported by the source CC through means of using another CC or a specific plug-in. Discussed in Section 2.

Objective
---------

PREFORMA requires at minimum a support for extending a CC through a CLI.

The purpose of this document is to work out the precise defintion of PIF.

SECTION 1 - PIA
============

### 1. CLI requirements

-	Ask for a single file check and produce a common XML report.
-	Attach conformance checkers using the shell CLI.
-	Get information about the Conformance Checkers.
-	Configure remote Conformance Checkers.

### 2. CLI Usage

For interoperability purposes, the shells will have two main execution modes.

-	Usage mode 1: For execution. With this mode, a single file (either tiff, pdf or movie) is passed to the application. The program starts a thread for processing it and returns a job id, so that we can make calls with this id to know the state of that job. 

-	Usage mode 2: For configuring the conformance checkers. We can request a list of connected conformance checkers, and adding, deleting and configuring them.

Each call produces an XML output, which is parsed by the shell that made the call, to continue its processing.

### 2.1. Execution mode

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

Ask for the state of a previously started job. The possible states can be: *Running*, *Finished*, *Waiting* or *Unknown*. If the state is *Running*, the field *progress* may contain the percentage of completion of the task. If the state is *Finished*, then the field *report* contains the path to the xml containing the report.

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

### 3. XML definition

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

### 4. Error codes (to be extended):

- 1001: No conformance checker found for this file
- 1002: Conformance checker not available
- 2001: Configuration not found

SECTION 2 - PEA
============

Guiding principles
------------------

### On the reason of enclosing the extension configuration with the Policy profile

The purpose of the PREFORMA Policy checker is to allow conformance to requirments that cannot be resolved through technical mechanisms. This refers to cases where the use of a technical functionality is facultative and its use is based on a user's preferences and not because it is tehnically valid or invalid. The ability to control this behavior is of utmost importance for government agencies and memory institutions, as they want to regulate the definition of a correct use of a file format.

The purpose of the PREFORMA CC is, in part, to address the fact that file format specifications leave room for interpretations, and technical implementations could take form in many different ways.

The PREFORMA project covers only three specific file formats: Matroska, PDF/A and TIFF (Baseline/IT/EP). Each format can however have one or more subsets of other file or resource formats, such as, embedded images and fonts, image encodings, ICC profiles, and attachments. Each of these formats may suffer from the same problems that the PREFORMA project attempts to address.

The PEA allows extending a CC to use other CCs to check the conformance of any additional formats in the source file format. Depending on the format there could be none, one or more alternatives for deciding how to validate the format. It follows that different users may implement different methods for handling which extensions to use. Consequently, a conformance check of a file using one and the same CC may result in different outcomes depending on how the CC's extensions are configured. This would be true regardless if a Policy is used or not.

The problem is therefore, if the configuration of the extension is dependent on the CC, in order to have a uniform conformance check of a file format the CC for the file format must be setup correctly. This would require additional information and documentation to be distributed by, e.g., a government agency, and understood by the users, in order to ensure a correct check of a file format.

One of the main principles of digital preservation is to contain all necessary components needed to recreate the original state of information within one and the same container.

By enclosing the extension configuration with a Policy profile all necessary information is provided in order to ensure that the file has been checked in accordance with the requirments decided by the policy maker.

Structure
---------

The PEA consist of the root element *extension*, which is a child of the policy root element. Each extension, that is, the child elements of *extension*, is defined within a *cc* element. 

Currently the Policy profile is an early draft and could be adjusted. Some of the elements could be more suitable as attributes.

Given the root element of the policy, the following subset *extension* is applied.
```XML
<?xml version="1.0" encoding="utf-8"?>
<policy>
  <extension>
    <cc>
```
```XML
      <signature type='[hex|ascii|iso8859-1|xmp|fext|uti|mime]'>[]</signature>
```
Which method to apply in order to make an initial, quick/fast, guess of the type of resource. The element value depends on the type of method applied, e.g., header information / magic number, XMP element, file extension, UTI, MIME.

Defining an appropriate method for defining multiple values may be necessary.

EXAMPLE
```XML
      <signature type='fext'>jpg|jpeg</signature>
```
```XML
      <signature type='XMP'>
        <pdfaid>part:1</pdfaid>
        <pdfaid>conformance:A</pdfaid>
      </signature>
```

```XML
      <priority>[unsigned int]</priority>
```

If multiple CCs for the same type of resource are allowed, an order of precedence is needed.

Could be an attribute of the element "cc", e.g., ```XML <cc priority='1'> ... </cc>```.

```XML
      <name>[string]</name>
```
The user's label for the purpose of the extension. Could be complemented with a *description* element.

```XML
      <version>[string]</version>
```
The version, release, edition or other form of identification of a variance of the extension.

This element could be an attribute of the element "exec", e.g., ```<exec version='1.0'>...</exec>```.

```XML
      <api>[string [cli|so|jar|include|import|...]]</api>
```
The interface to the extension. All CCs have to have support for a CLI.

This element could be an attribute of the element "exec", e.g., ```<exec api='jar'>...</exec>```.

```XML
      <path>[string [file]]</path>
```
The path to the file which executes or initializes the extension.

```XML
      <arg>[string []]</arg>
```
The arguments, switches, options, or parameters, if any, to be passed to the extension.

This element could be an attribute of the element "exec", e.g., ```<exec arg='-x -y'>...</exec>```.

```XML
  <report>[string [directory]]</report>
```

The directory where the report from the extension is saved. If omitted, then defaults to the source CC's report directory.

```XML
  <style>[string [ file [css|xsl|...]]]</style>
```

The style sheet to apply when formatting the report from the extension. Omit to default to the source CC's stylesheet.

The style element could be an attribute of the element "report", e.g., ```<report style='./../style.css'>...</report>```.

```XML
  <policy>[string [file]]</policy>
```

The policy to apply for the extension. A policy is specific to a CC and is created according to the rules by that CC. The source CC only points to the policy which belongs to a specific CC extending the source CC; it does not necessary supports the creation or editing of other extension's policies.

```XML
  <...>[]</...>
```
Additional elements that may be required, e.g., such as "timeout".


```XML
  </extension>
  <!-- The policy elements -->
</policy>
```


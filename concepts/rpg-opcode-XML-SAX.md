---
title: XML-SAX (Parse an XML Document)
section: Operations, Expressions, and Functions
---

# XML-SAX (Parse an XML Document)

Free-Form Syntax: XML-SAX{(E)} %HANDLER(handlerProc : commArea) %XML(xmlDoc {: options });

## Code

| Factor 1 | Extended Factor 2 |
|----------|------------------|
| XML-SAX{(E)} | %HANDLER(handlerProc : commArea) %XML(xmlDoc {: options }) |

## Description

Tip: If you are not familiar with the basic concepts of XML and of processing XML documents, you may find it helpful to read the "Processing XML Documents" section in Rational Development Studio for i: ILE RPG Programmer's Guide before reading further in this section.

XML-SAX initiates a SAX parse for an XML document. The XML-SAX operation code begins by calling an XML parser which begins to parse the document. When an event occurs such as the parser finding the start of an element, finding an attribute name, finding the end of an element and so on, the parser calls the handling procedure handlerProc with parameters describing the event. When the handling procedure returns, the parser continues to parse until it finds the next event and calls the handling procedure again. When the parser has finished parsing the document, control passes to the statement following the XML-SAX operation.

The first operand must be the %HANDLER built-in function; handlerProc is a prototype name that specifies the procedure to be called to handle the SAX events and commArea is the communication-area parameter to be passed by the parser to the handling procedure. The communication-area parameter must be the same type as the first prototyped parameter of the handling procedure. It provides a way for the procedure specifying the XML-SAX operation code to communicate with the handling procedure, and for the handling procedure to save information related to the parse from one event to the next.

See "%HANDLER (handlingProcedure : communicationArea )" on page 693 for more information on %HANDLER.

The second operand must be the %XML built-in function, identifying the XML document to be parsed and the options controlling the way the parsing is done. See "%XML (xmlDocument {:options})" on page 768 for more information on %XML.

Operation extender E can be specified to handle the following status codes:

- 00351: Error in XML parsing
- 00352: Invalid XML option
- 00354: Error preparing for XML parsing

For status 00351, the return code from the parser will be placed in the subfield "External return code" in positions 368-371 of the PSDS. This subfield will be set to zero at the beginning of the operation and set to the value returned by the parser at the end of the operation. This subfield is relevant only in a module that has an XML-SAX operation. SAX event-handling procedures receive the information from the parser as parameters.

The event-handling procedure will not be called if an exception occurs before parsing begins. For example, if the specified file is not found, the operation will end immediately with status 00354 and the event-handling procedure will never get control.

If an error occurs during parsing, the handling procedure will be called with a *XML_EXCEPTION event, and when the handling procedure returns, parsing will end and the XML-SAX operation will fail with status code 00351. The return code from the parser will be placed in the "External return code" subfield in positions 368 - 371 of the PSDS.

If an unknown, invalid or unrelated option is found in the %XML options string, XML-SAX will fail with status code 00352. The External return code subfield in positions 368 - 371 of the PSDS will not be updated from the initial value of zero, set when the operation begins.

### %XML options for the XML-SAX operation code

#### doc (default string)

The doc option indicates what the source operand of %XML contains.

- string indicates that the source operand contains XML data
- file indicates that the source operand contains an IFS file name

 // In the following example, the first parameter
 // of %XML is the name of a file.  Option
 // "doc=file" must be specified.
 ifsfile = 'myfile.xml';
 opt = 'doc=file';
 XML-SAX %handler(hdlr:comm) %XML(ifsfile : opt);
 // In the following example, the first parameter
 // of %XML is an XML document.  Since the "doc"
 // option defaults to "string", no options are
 // necessary.
 xmldata = '<data><num>3</num></data>';
 XML-SAX %handler(hdlr:comm) %XML(xmldata);

Figure 397. Example of the doc option:

#### ccsid (default job)

The ccsid option specifies the CCSID that the XML data should be returned in.

- job indicates that the XML parser should return data in the job CCSID. This is the CCSID that the RPG compiler uses for character data in the program.
- ucs2 indicates that the XML parser should return data in the UCS-2 CCSID of the module.
- numeric value indicates that the XML parser should return the data in the specified CCSID. In this case, it is up to the RPG programmer to ensure that the data is handled correctly within the RPG program. The RPG compiler will assume that character data is in the job CCSID.

 // In the following example, the data is to be
 // returned in the job ccsid.  Even though the
 // default for the "ccsid" option is "job", it
 // is valid to specify it explicitly.
 XML-SAX %handler(hdlr:comm) %XML(xmlString : 'ccsid=job');
 // In the following example, the data is to be
 // returned in UCS-2.
 opt = 'ccsid=ucs2';
 XML-SAX %handler(hdlr:comm) %XML(xmldata : opt);
 // In the following example, the data is to be
 // returned in UTF-8.  The handling procedure must
 // exercise caution to convert the data to some CCSID
 // that the program can handle, if the data is to be
 // used within the handling procedure.
 XML-SAX %handler(hdlr:comm) %XML(xmldata : 'ccsid=1208');

Figure 398. Example of the ccsid option:

Note: For *XML_UCS2_REF and *XML_ATTR_UCS2_REF events, the data is always returned as a UCS-2 value independent of the ccsid option.

### XML-SAX event-handling procedure

The event-handling procedure is a user-written prototyped procedure. It must have the following return type and parameters:

| Parameter number or return value | Data type and passing mode | Description |
|--------------------------------|---------------------------|-------------|
| Return value | 4-byte integer (10I 0) | Returning a value of zero indicates that parsing should continue; returning any other value indicates that parsing should end. |
| 1 - Communication area | Any type, passed by reference | Used to communicate between the XML-SAX operation and the handler, and between successive calls to the handler. |
| 2 - Event | 4-byte integer (10I 0), passed by value | The XML event discovered by the parser. Special words such as *XML_START_ELEMENT can be used to identify the events within the handling procedure. See "XML events" on page 1019. |
| 3 - Data | Pointer (*), passed by value | If this parameter is not relevant to the event, it will have a value of *NULL. Otherwise, it will point to the data for the event. For the *XML_UCS2_REF, and *XML_ATTR_UCS2_REF events, the data will always be UCS-2 data. For all other events, the data will be in the CCSID specified by the "ccsid" option of the %XML built-in function. |
| 4 - Length | 8-byte integer (20I 0), passed by value | For most events, this is the length of the data pointed to by the third parameter, in bytes. If this parameter is not relevant for a particular event, it will have the value -1. If the data is being returned in UCS-2 due to the "ccsid" option of the %XML built-in function, this value must be divided by two to obtain the number of UCS-2 characters. For the *XML_EXCEPTION event, this parameter will have the length of the document that was parsed when the error occurred. |
| 5 - Exception ID | 4-byte integer (10I 0), passed by value | The exception ID. For all events other than *XML_EXCEPTION, this parameter will have a value of zero. See the section on XML return codes in the Rational Development Studio for i: ILE RPG Programmer's Guide. |

See "%HANDLER (handlingProcedure : communicationArea )" on page 693 for more information on %HANDLER.

 D saxHandler      pr            10i 0
 D   commArea                          likeds(myCommArea)
 D   event                       10i 0 value
 D   string                        *   value
 D   stringlen                   20i 0 value
 D   exceptionId                 10i 0 value

Figure 399. Sample prototype for an XML-SAX handling procedure

### XML events

During the SAX parse of your XML document, several XML events will be passed to your XML-SAX handling procedure. To identify the events within your procedure, use the special names starting with *XML, for example *XML_START_ELEMENT.

For most events, the handling procedure will be passed a value associated with the event. For example, for the *XML_START_ELEMENT event, the value is the name of the XML element.

**Table 150. XML events**

**1. Events discovered before the first XML element**

| Event | Value |
|-------|-------|
| *XML_START_DOCUMENT | Indicates that parsing has begun |
| *XML_VERSION_INFO | The "version" value from the XML declaration |
| *XML_ENCODING_DECL | The "encoding" value from the XML declaration |
| *XML_STANDALONE_DECL | The "standalone" value from the XML declaration |
| *XML_DOCTYPE_DECL | The value of the Document Type Declaration |

**2. Events related to XML elements**

| Event | Value |
|-------|-------|
| *XML_START_ELEMENT | The name of the XML element that is starting |
| *XML_CHARS | The value of the XML element |
| *XML_PREDEF_REF | The value of a predefined reference |
| *XML_UCS2_REF | The value of a UCS-2 reference |
| *XML_UNKNOWN_REF | The name of an unknown entity reference |
| *XML_END_ELEMENT | The name of the XML element that is ending |

**3. Events related to XML attributes**

| Event | Value |
|-------|-------|
| *XML_ATTR_NAME | The name of the attribute |
| *XML_ATTR_CHARS | The value of the attribute |
| *XML_ATTR_PREDEF_REF | The value of a predefined reference |
| *XML_ATTR_UCS2_REF | The value of a UCS-2 reference |
| *XML_UNKNOWN_ATTR_REF | The name of an unknown entity reference |
| *XML_END_ATTR | Indicates the end of the attribute |

**4. Events related to XML processing instructions**

| Event | Value |
|-------|-------|
| *XML_PI_TARGET | The name of the target |
| *XML_PI_DATA | The value of the data |

**5. Events related to XML CDATA sections**

| Event | Value |
|-------|-------|
| *XML_START_CDATA | The beginning of the CDATA section |
| *XML_CHARS | The value of the CDATA section |
| *XML_END_CDATA | The end of the CDATA section |

**6. Other events**

| Event | Value |
|-------|-------|
| *XML_COMMENT | The value of the XML comment |
| *XML_EXCEPTION | Indicates that the parser discovered an error |
| *XML_END_DOCUMENT | Indicates that parsing has ended |

This sample XML document is referred to in the descriptions of the XML events.

 <?xml version="1.0" encoding="ibm-1140" standalone="yes" ?>
 <!DOCTYPE page [
   <!ENTITY abc "ABC Inc">
 ]>
 <!-- This document is just an example  -->
 <sandwich>
   <bread type="baker's best" supplier="&abc;" />
   <?spread   please use real mayonnaise ?>
   <spices attr="&#x2B;">Salt &amp; pepper</spices>
   <filling>Cheese, lettuce,
            tomato, &#0061; &xyz;
   </filling>
   <![CDATA[We should add a <relish> element in future!]]>
 </sandwich>junk

Figure 400. Sample XML document referred to in the descriptions of the XML events

#### *XML_START_DOCUMENT

This event occurs once, at the beginning of parsing the document. Only the first two parameters are relevant for this event. Accessing the String parameter will cause a pointer-not-set error to occur.

#### *XML_VERSION_INFO

This event occurs if the XML declaration contains version information. The value of the string parameter is the version value from the XML declaration.

From the example: '1.0'

#### *XML_ENCODING_DECL

This event occurs if the XML declaration contains encoding information. The value of the string parameter is the encoding value from the XML declaration.

From the example: 'ibm-1140'

#### *XML_STANDALONE_DECL

This event occurs if the XML declaration contains standalone information. The value of the string parameter is the standalone value from the XML declaration.

From the example: 'yes'

#### *XML_DOCTYPE_DECL

This event occurs if the XML declaration contains a DTD (Document Type Declaration). Document type declarations begin with the character sequence '<!DOCTYPE' and end with a '>' character.

Note: This is the only event where the XML text includes the delimiters.

The value of the string parameter is the entire DOCTYPE value, including the opening and closing character sequences.

From the example: '<!DOCTYPE page [LF  <!ENTITY abc "ABC Inc">LF]>' (LF represents the LINE FEED character.)

#### *XML_START_ELEMENT

This event occurs once for each element tag or empty element tag. The value of the string parameter is the element name.

From the example, in the order they appear: 1. 'sandwich' 2. 'bread' 3. 'spices' 4. 'filling'

#### *XML_CHARS

This event occurs for each fragment of content. Content normally consists of a single string, even if the text is on multiple lines. It is split into multiple events if it contains references. The value of the string parameter is the fragment of the content.

From the example:
1. 'Salt '
2. ' pepper'
3. 'Cheese, lettuce,WWWtomato, ', where WWW represents several "whitespace" characters. See the Notes section.
4. 'We should add a <relish> element in future!'

Note:
1. The content fragment '&amp;' causes a *XML_PREDEF_REF event, and the fragment '&#0061;' causes a *XML_UCS2_REF event.
2. If the value spans multiple lines of the XML document, it will contain end-of-line characters and it will possibly contain unwanted series of blanks. In the example, "lettuce," and "tomato" are separated by a line-feed character and several blanks. These characters are called whitespace; whitespace is ignored if it appears between XML elements, but it is considered to be data if it appears within an element. If it is possible that the XML data may contain unwanted whitespace, the data may need to be trimmed before use. To trim unwanted leading and trailing whitespace, use the following coding. See example Figure 404 on page 1030.

 * x'15'=newline  x'05'=tab     x'0D'=carriage-return
 * x'25'=linefeed x'40'=blank
 D whitespaceChr   C                   x'15050D2540'
 /free
     temp = %trim(value : whitespaceChr);

#### *XML_PREDEF_REF

This event occurs when content has one of the predefined single-character references '&amp;', '&apos;', '&gt;', '&lt;', and '&quot;'. The value of the string parameter is the single-byte character:

| Reference | Character |
|-----------|-----------|
| &amp; | & |
| &apos; | ' |
| &gt; | < |
| &lt; | > |
| &quot; | " |

Note: The string is a UCS-2 character if the parsing is being done in UCS-2.

From the example: '&', from the content for the "spices" element.

#### *XML_UCS2_REF

This event occurs when content has a reference of the form '&#dd..;' or '&#xhh..;', where 'd' and 'h' represent decimal and hexadecimal digits, respectively. The value of the string parameter is the UCS-2 value of reference.

Note: This parameter is a UCS-2 character (type C) even if the parsing is being done in single-byte character.

From the example: The UCS-2 value '=', appearing as "&#0061;", from the fragment at the end of the "filling" element.

#### *XML_UNKNOWN_REF

This event occurs for an entity reference appearing in content, other than the five predefined entity references as shown for *XML_PREDEF_REF above. The value of the string parameter is the name of the reference; the data that appears between the opening '&' and the closing ';'.

From the example: 'xyz'

#### *XML_END_ELEMENT

This event occurs when the parser finds an element end tag or the closing angle bracket of an empty element. The value of the string parameter is the element name.

From the example, in the order they occur: 1. 'bread' 2. 'spices' 3. 'filling' 4. 'sandwich'

#### *XML_ATTR_NAME

This event occurs once for each attribute in an element tag or empty element tag, after recognizing a valid name. The value of the string parameter is the attribute name.

From the example, in the order they appear: 1. 'type' 2. 'supplier' 3. 'attr'

#### *XML_ATTR_CHARS

This event occurs for each fragment of an attribute value. An attribute value normally consists of a single string, even if the text is on multiple lines. It is split into multiple events if it contains references. The value of the string parameter is the fragment of the attribute value.

From the example, in the order they appear: 1. 'baker' 2. 's best'

Note:
1. The fragment '&apos;' causes a *XML_ATTR_PREDEF_REF event
2. See the discussion on *XML_CHARS for recommendations for handling unwanted end-of-line characters and unwanted blanks.

#### *XML_ATTR_PREDEF_REF

This event occurs when an attribute value has one of the predefined single-character references '&amp;', '&apos;', '&gt;', '&lt;', and '&quot;'. The value of the string parameter is the single-byte character:

| Reference | Character |
|-----------|-----------|
| &amp; | & |
| &apos; | ' |
| &gt; | > |
| &lt; | < |
| &quot; | " |

Note: The string is a UCS-2 character if the parsing is being done in UCS-2.

From the example, the value for the "type" attribute: ' (The apostrophe character, "&apos")

#### *XML_ATTR_UCS2_REF

This event occurs when an attribute value has a reference of the form '&#dd..;' or '&#xhh..;', where 'd' and 'h' represent decimal and hexadecimal digits, respectively. The value of the string parameter is the UCS-2 value of the reference.

Note: This parameter is a UCS-2 character (type C) even if the parsing is being done in single-byte character.

From the example, from the value of the "attr" attribute: The UCS-2 value '+', appearing as "&#x2B;" in the document.

#### *XML_UNKNOWN_ATTR_REF

This event occurs for an entity reference appearing in an attribute, other than the five predefined entity references as shown for *XML_ATTR_PREDEF_REF above. The value of the string parameter is the name of the reference; the data that appears between the opening '&' and the closing ';'.

From the example: 'abc'

Note: The parser does not parse the DOCTYPE declaration, so even though entity "abc" is defined in the DOCTYPE declaration, it is considered undefined by the parser.

#### *XML_END_ATTR

This event occurs when the parser reaches the end of an attribute value. The string parameter is not relevant for this event. Accessing the string parameter will cause a pointer-not-set error to occur.

From the example: For the attribute type="baker&apos;s best", the *XML_END_ATTR event occurs after all three parts of the attribute value ("baker", &apos; and "s best") have been handled.

#### *XML_PI_TARGET

This event occurs when the parser recognizes the name following the processing instruction (PI) opening character sequence '<?'. Processing instructions allow XML documents to contain special instructions for applications. The value of the string parameter is the processing instruction name.

From the example: 'spread'

#### *XML_PI_DATA

This event occurs for the data part of a processing instruction, up to but not including the PI closing character sequence '?>'. The value of the string parameter is the processing instruction data, including trailing but not leading white space.

From the example: 'please use real mayonnaise '

Note: See the discussion for *XML_CHARS for recommendations for handling unwanted end-of-line characters and unwanted blanks.

#### *XML_START_CDATA

This event occurs when a CDATA section begins. CDATA sections begin with the string '<![CDATA[' and end with the string ']]>'. Such sections are used to "escape" blocks of text containing characters that would otherwise be recognized as XML markup. The parser passes the content of a CDATA section between these delimiters as a single *XML_CHARS event. The value of the string parameter is always the opening character sequence '<![CDATA['.

From the example: '<![CDATA['

#### *XML_END_CDATA

This event occurs when a CDATA section ends. The value of the string parameter is always the closing character sequence ']]>'.

From the example: ']]>'

#### *XML_COMMENT

This event occurs for any comments in the XML document. The value of the string parameter is the data between the opening delimiter '<!--' and the closing delimiter '-->' , including leading and trailing white space.

From the example: ' This document is just an example '

#### *XML_EXCEPTION

This event occurs when the parser detects an error. The value of the string parameter is the "String" parameter is not relevant for this event. Accessing the String parameter will cause a pointer-not-set error to occur. The value of the string-length parameter is the length of the document that was parsed up to and including the point where the exception occurred. The value of the Exception-Id parameter is the exception ID as assigned by the parser. The meaning of these exceptions is documented in the section on XML return codes in the Rational Development Studio for i: ILE RPG Programmer's Guide.

From the example: An exception event would occur when the parser encountered the word "junk", which is non-whitespace data appearing after the end of the XML document. (The XML document ends with the end-element tag for the "sandwich" element.)

#### *XML_END_DOCUMENT

This event occurs when parsing has completed. Only the first two parameters are relevant for this event. Accessing the String parameter will cause a pointer-not-set error to occur.

Note: To aid in debugging an XML-SAX handling procedure, the Control specification keyword DEBUG(*XMLSAX) can be specified. For more details on this keyword, see "DEBUG{(*DUMP | *INPUT | *RETVAL | *XMLSAX | *NO | *YES)}" on page 368 and the Debugging chapter in the Rational Development Studio for i: ILE RPG Programmer's Guide. For more information about XML parsing, including limitations of the XML parser used by RPG, see the XML chapter in the Rational Development Studio for i: ILE RPG Programmer's Guide.

### Examples of the XML-SAX operation

 D xmlString       S               C    '<?xml version="1.0"> +
 D                                      <elem>data</elem>'
 D psds            DS
 D   xmlRc                       10I 0  OVERLAY(psds:368)
 /free
    // The XML is in an IFS file.  The "option" operand of %XML specifies
    // that the document operand is the name of an IFS file.
    XML-SAX %HANDLER(mySaxHandler : myHandlerInfo)
            %XML('/home/myuserid/myxml.xml' : 'doc=file');
    // The XML is in a string.  The "option" operand of %XML is not specified.
    XML-SAX %HANDLER(mySaxHandler : myHandlerInfo) %XML(xmlString);

Figure 401. XML-SAX operations in Free-form calculations

 CL0N01Factor1+++++++Opcode&ExtExtended-Factor2+++++++++++++++++++++++++
 C                   XML-SAX   %HANDLER(mySaxHandler : myHandlerInfo)
 C                             %XML('/home/myuserid/myxml.xml' : 'doc=file')
 C                   XML-SAX   %HANDLER(mySaxHandler : myHandlerInfo)
 C                             %XML(xmlString)

Figure 402. XML-SAX operations in Fixed-form calculations

 H DEBUG(*XMLSAX)
 Fqsysprt   o    f  132        printer
 * The xmlRc subfield will be set to a non-zero value
 * if the XML-SAX operation fails because of an error
 * discovered by the parser
 D psds           SDS
 D   xmlRc                       10I 0  OVERLAY(psds:368)  [1] 
 D qsysprtDs       DS           132
 * This data structure defines the type for the parameter
 * passed to the SAX handling procedure.
  [2] 
 D value_t         S             50A    VARYING
 D handlerInfo_t   DS                   QUALIFIED
 D                                      BASED(dummy)
 D   pValue                        *
 D   numAttendees                 5P 0
 D   name                               LIKE(value_t)
 D   company                            LIKE(value_t)
 D   alwExtraAttr                 1N
 D   handlingAttrs...
 D                                 N
 * Define a specific instance of the handlerInfo_t data
 * structure and the prototype for the handler
 D myHandlerInfo   DS                   LIKEDS(handlerInfo_t)
 D mySaxHandler    PR            10i 0
 D   info                               LIKEDS(handlerInfo_t)
 D   event                       10i 0  VALUE
 D   stringPtr                     *    VALUE
 D   stringLen                   20i 0  VALUE
 D   exceptionId                 10i 0  VALUE
 /free
     monitor;
        // Start XML parsing
        // Indicate that the handler should not allow
        // any unexpected attributes in the XML elements.
        myHandlerInfo.alwExtraAttr = *OFF;  [3] 
        XML-SAX %HANDLER(mySaxHandler : myHandlerInfo)
                %XML('/home/myuserid/myxml.xml' : 'doc=file');
        // The XML parse completed normally
        // Results are passed back in the communication
        // area specified by the %HANDLER built-in function
        qsysprtDs = 'There are '
                  + %CHAR(myHandlerInfo.numAttendees)
                  + ' attendees.';
     on-error 00351;
        // The XML parse failed with a parser error.
        // The return code from the parser is in the PSDS.
        qsysprtDs = 'XML parser error: rc='
                  + %CHAR(xmlRc)
                  + '.';
     endmon;
     write qsysprt qsysprtDs;
     *inlr = '1';
 /end-free
 P mySaxHandler    B
 D                 PI            10i 0
 D   info                               LIKEDS(handlerInfo_t)
 D   event                       10i 0  VALUE
 D   stringPtr                     *    VALUE
 D   stringLen                   20i 0  VALUE
 D   exceptionId                 10i 0  VALUE
 D value           S                    LIKE(value_t)
 D                                      BASED(info.pValue)
 D chars           S          65535A    BASED(stringPtr)
 D ucs2            S          16383C    BASED(stringPtr)
 D ucs2Len         S             10i 0
 /free
  select;
 // start parsing
     when event = *XML_START_DOCUMENT;  [4] 
     clear info;
     // start processing an attendee, by indicating
     // that subsequent calls to this procedure should
     // handle XML-attribute events.
     when event = *XML_START_ELEMENT;
       if %subst(chars : 1 : stringLen) = 'attendee';
          info.handlingAttrs = *ON;  [5] 
          info.name = '';
          info.company = '';
          info.numAttendees += 1;
       endif;
     // display information about the attendee
     when event = *XML_END_ELEMENT;
       if %subst(chars : 1 : stringLen) = 'attendee';
          info.handlingAttrs = *OFF;
          qsysprtDs = 'Attendee '
                    + info.name
                    + ' is from company '
                    + info.company;
          write qsysprt qsysprtDs;
       endif;
     // prepare to get an attribute value by setting
     // a basing pointer to the address of the correct
     // variable to receive the value
     when event = *XML_ATTR_NAME;
       if info.handlingAttrs;
         if %subst(chars : 1 : stringLen) = 'name';
            info.pValue = %addr(info.name);
         elseif %subst(chars : 1 : stringLen) = 'company';
            info.pValue = %addr(info.company);
         else;
            // If the XML element is not expected to have
            // extra attributes, halt the parsing by
            // returning -1.
            if not info.alwExtraAttr;
               qsysprtDs = 'Unexpected attribute '
                        + %subst(chars : 1 : stringLen)
                        + ' found.';
               write qsysprt qsysprtDs;
               return -1;  [6] 
            endif;
            info.pValue = *NULL;
         endif;
      endif;
 // handle an exception
     when event = *XML_EXCEPTION;
         qsysprtDs = 'Exception '
                  + %char(exceptionId)
                  + ' occurred.';
         write qsysprt qsysprtDs;
         return exceptionId;
     other;
       // If this is an attribute we are interested
       // in, the basing pointer for "value" has been
       // set to point to either "name" or "company"
       // Append each fragment of the value to the
       // current data
       if  info.handlingAttrs
       and info.pValue <> *NULL;
         if event = *XML_ATTR_CHARS
         or event = *XML_ATTR_PREDEF_REF;
            value += %subst(chars : 1 : stringLen);
         elseif event = *XML_ATTR_UCS2_REF;
            ucs2Len = stringLen / 2;  [7] 
            value += %char(%subst(ucs2 : 1 : ucs2Len));
         endif;
       endif;
     endsl;
     return 0;  [8] 
 /end-free
 P mySaxHandler    E

This example illustrates several features of SAX parsing.

1. The "External Return Code" subfield of the PSDS, named xmlRc here.
2. The communication area data structure, used to communicate between the XML-SAX operation and the SAX event-handling procedure.
3. The XML-SAX operation initiates the parsing of the XML document.
4. The SAX event-handling procedure compares the event parameter to the special names *XML_START_DOCUMENT etc.
5. The communication area is also used for the event-handling procedure to communicate with itself between calls.
6. The event-handling procedure discovers an error and halts the parsing by returning -1.
7. The *XML_ATTR_UCS2_REF event has UCS-2 data, independent of the CCSID that is normally used to return data for this XML-SAX operation. The length represents the number of bytes in the data, so it must be divided by two to obtain the number of UCS-2 characters.
8. If the event-handling procedure does not discover any errors, it returns 0, indicating that parsing should continue.

The following sample XML document could be used with this example.

 <meeting>
   <attendee name="Jack" company="A&amp;B Electronics"/>
   <attendee company="City&#x2B; Waterworks" name="Jill"/>
   <attendee name="Bill" company="Ace Movers" extra="yes"/>
 </meeting>

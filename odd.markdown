---
layout: page
title: ODD Customization Assignment
permalink: /odd/
---

## Introduction
The TEI Guidelines are a set of agreed-upon standards used by a large
segment of the Digital Humanities community to encode textual material. The use 
of standards ensures maximal reusability of your work.

But, as a widely used standard, the TEI must accommodate many approaches to digitizing
texts, some of which you may not want or need in your project. It can be very helpful 
(for reasons we will discuss in class) to use a customization of the TEI
rather than the entire P5 All guidelines. This assignment will teach you 
why it is desirable to customize the TEI and how to do so.

## To Submit for Evaluation
- You will upload to GitHub an ODD file customizing the TEI guidelines 
for the use of your particular project. It should include 
the following: 
	- Restriction of the elements used in your project.
	- Restriction of the attributes allowed on one or more elements.
	- A closed list of attribute values on one or more attributes.
	- Validating some aspect of your document against an external 
	list of values.
	- One or more embedded schematron rules.
	- For each element you customize (using the 
	[\<elementSpec\>](https://www.tei-c.org/release/doc/tei-p5-doc/en/html/ref-elementSpec.html){:target="_blank"} 
	element), you must include an 
	[\<exemplum\> ](https://www.tei-c.org/release/doc/tei-p5-doc/en/html/ref-exemplum.html){:target="_blank"}
	element with a snippet of code exemplifying 
	the customization and a 
	[\<remarks\> ](https://www.tei-c.org/release/doc/tei-p5-doc/en/html/ref-remarks.html){:target="_blank"}
	element offer a prose description 
	of how your project is using that element.
- You will use oXygen XML editor to transform your ODD into RelaxNG, upload that GitHub, 
and validate your TEI file(s) against that RelaxNG. 


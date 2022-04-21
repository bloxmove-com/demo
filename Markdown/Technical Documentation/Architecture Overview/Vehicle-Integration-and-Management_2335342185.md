<div id="page">

<div id="main" class="aui-page-panel">

<div id="main-header">

<div id="breadcrumb-section">

1.  <span>[BloXmove Dev](index.html)</span>
2.  <span>[Architecture
    Overview](Architecture-Overview_4492492808.html)</span>

</div>

# <span id="title-text"> BloXmove Dev : Vehicle Integration and Management </span>

</div>

<div id="content" class="view">

<div class="page-metadata">

Created by <span class="author"> Jan-Paul Buchwald (Unlicensed)</span>,
last modified on Aug 05, 2021

</div>

<div id="main-content" class="wiki-content group">

This page collects architectural options and input for the management
and integration of vehicles including related data and services.

## Fleet Gateway

Input provided by Leam

<div class="table-wrap">

|                        |                                                                                                                                                   |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| REST API Specification | <span class="confluence-embedded-file-wrapper">[![](attachments/thumbnails/2335342185/2335342196)](attachments/2335342185/2335342196.pdf)</span>  |
| Architectural Scheme   | <span class="confluence-embedded-file-wrapper">[![](attachments/thumbnails/2335342185/2335407723)](attachments/2335342185/2335407723.pptx)</span> |

</div>

Architecture corresponds to a combination / subset of existing
(simulated) Fleet Backend and Virtual Car Wallet components, detailed
integration with MBP is not yet defined.

<span class="confluence-embedded-file-wrapper image-center-wrapper confluence-embedded-manual-size">![](attachments/2335342185/2335637100.png?width=238)</span>

## Vehicle Onboard Unit Integration

Input provided by Nantis

<div class="table-wrap">

|                                                                                                                                                   |                              |
| ------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------- |
| <span class="confluence-embedded-file-wrapper">[![](attachments/thumbnails/2335342185/2335735409)](attachments/2335342185/2335735409.docx)</span> | Concept Document Version 0.2 |

</div>

Focusses on integration of physical car wallet based on OLU (On-board
Logic Unit) Connect Box.

Suggested approach in two steps: first access / integration via a
“Virtual Car Application” (corresponds to Virtual Car Wallet), then
direct handling in Physical Car Application

<span class="confluence-embedded-file-wrapper image-center-wrapper">![](attachments/2335342185/2335506027.png)</span><span class="confluence-embedded-file-wrapper image-center-wrapper">![](attachments/2335342185/2335833707.png)</span>

## Hackathon Prototype With Sodacars

Integration of a real Smart vehicle using the Soda
(<https://sodacar.com/>) Mobility Platform Backend and an xToolTech TBox
based on the existing Virtual Car Wallet Architecture and Fleet2Share
scenario.

<span class="confluence-embedded-file-wrapper image-center-wrapper">![](attachments/2335342185/2335309423.png)</span>

Code for Virtual Car Wallet Extension allowing flexible Fleet Gateway
Adapters, as an example implementing a *SodaAdapter* accessing a REST
API provided by Soda:

<https://github.com/bloxmove-com/virtual-car-wallet/blob/master/src/adapters/soda-adapter.class.ts>

### Architecture and Requirements for a production-ready Setup

The scenario outlined in the Hackathon can be simplified by controlling
both the read access (getting telematics data) and the “write” access
(changing state of the car, in our scenario open/close car doors)
through the virtual car wallet. This would result in the following basic
architecture and requirements for a SODA telematics gateway that can be
called by the MBP virtual car wallet component.

<span class="confluence-embedded-file-wrapper image-center-wrapper">![](attachments/2335342185/4446126123.png)</span>

</div>

<div class="pageSection group">

<div class="pageSectionHeader">

## Attachments:

</div>

<div class="greybox" data-align="left">

![](images/icons/bullet_blue.gif)
[20201006-MBP\_Fleet\_Gateway\_API.pdf](attachments/2335342185/2335342196.pdf)
(application/pdf)  
![](images/icons/bullet_blue.gif) [Sharing Fleet MBP\_ T-Box Integration
Prinzipskizze.pptx](attachments/2335342185/2335407723.pptx)
(application/vnd.openxmlformats-officedocument.presentationml.presentation)  
![](images/icons/bullet_blue.gif)
[image-20201110-121010.png](attachments/2335342185/2335735403.png)
(image/png)  
![](images/icons/bullet_blue.gif)
[image-20201110-121028.png](attachments/2335342185/2335637100.png)
(image/png)  
![](images/icons/bullet_blue.gif) [MBP - OLU Client -
Concept.nantis.docx](attachments/2335342185/2335735409.docx)
(application/vnd.openxmlformats-officedocument.wordprocessingml.document)  
![](images/icons/bullet_blue.gif)
[image-20201110-121612.png](attachments/2335342185/2335604333.png)
(image/png)  
![](images/icons/bullet_blue.gif)
[OLU-Virtual.png](attachments/2335342185/2335506027.png) (image/png)  
![](images/icons/bullet_blue.gif)
[OLU-Direct.png](attachments/2335342185/2335833707.png) (image/png)  
![](images/icons/bullet_blue.gif)
[Wanxiang-Hackathon-Architecture.png](attachments/2335342185/2335309423.png)
(image/png)  
![](images/icons/bullet_blue.gif) [2021-05-06-SODA Telematics
Gateway.png](attachments/2335342185/4334780636.png) (image/png)  
![](images/icons/bullet_blue.gif) [2021-06-03-SODA Telematics
Gateway.png](attachments/2335342185/4446126123.png) (image/png)  

</div>

</div>

</div>

</div>

<div id="footer" data-role="contentinfo">

<div class="section footer-body">

Document generated by Confluence on Apr 20, 2022 13:42

<div id="footer-logo">

[Atlassian](http://www.atlassian.com/)

</div>

</div>

</div>

</div>

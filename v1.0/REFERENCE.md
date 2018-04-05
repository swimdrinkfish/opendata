# Technical Definition

## Summary

The data interchange format for publishing records that are compliant with this specification is JSON. Due to broad platform and development library support, JSON is increasingly becoming the de facto standard format for data exchange on the internet. Records can be processed in environments ranging from a simple web browser to native mobile devices to a complex clusters of dedicated servers.

### Validation

Ensuring data integrity and standard compliance is of paramount concern when exchanging data of this nature. To this end, this standard utilizes the *JSON Schema* mechanism to validate records being exchanged. *JSON Schema *is an IETF proposed standard for validating the formatting of a received JSON data, and is equivalent to the standardized XSD mechanism for annotating and validating XML documents.

The *JSON Schema *standard has iterated through multiple draft implementations since an initial release in 2009 and as of November of 2017 has not yet been finalized. As such, this recreational water quality data exchange standard is implemented against draft-04 of the *JSON Schema *specification. The decision use draft-04 was based on widespread toolset, validation library, and document editing support. This decision may be revisited as the relevant tools evolve and the standard continues to progress towards finalization.

### Globally Unique Identification

One of the key concepts of this standard is the notion of assigning a globally unique identification (GUID) to each water quality sample described. This allows for records expressed using this standard to be freely stored, forwarded, shared, and then easily de-duplicated, tracked, and referenced by any entity ultimately receiving and processing the records.

The format for a GUID entry is as follows:

`Reverse FQDN of Original Publisher/Locally Unique Identifier`

For example, a water quality sample document published by Lake Ontario Waterkeeper (waterkeeper.ca) for a beach called "Bathurst Quay" may appear as follows:

`ca.waterkeeper/bathurst-2017-11-20-A1`

The `Reverse FQDN of Original Publisher` portion is a string, conforming to the standard reverse FQDN format already used by various programming languages and document implementations (for example, Java package naming schemes). As with all domain name variants, the required length of the first label is between **2** and **6** characters and the maximum length of any subsequent label is 64 characters (including the preceding "." character). Only characters valid in domain names (a-z, A-Z, 0-9, and -) are valid.  

The `Locally Unique Identifier portion` is a string with no specified format other than a limitation to characters valid in domain names (a-z, A-Z, 0-9, and -) and a maximum length of 64 characters. 

An organization publishing records in this standard can choose any identifier format that allows them to uniquely identify a sample record within their own proprietary data storage mechanisms.

The schema files provided as part of this standard enforce the described GUID formatting using a regex validation mechanism, and this validation scheme should be adequately supported by any software mechanism employed.

### Sample Result Revocation and Referencing

As each sample described by this standard is assigned a globally unique identification (GUID) by the original publisher, this standard allows two methods by which records being issued may reference other existing records.

**Revocation:** A record issued in this standard can indicate that a previously issued record has been effectively "revoked". This is useful in instances where a publishing entity or organization is correcting previously issued record data, or is issuing new record data using more precise/accurate measurement methods. For example, a record issued using a predictive modelling method and be revoked by a second record when laboratory sample data becomes available.

Please note that it is considered a violation of the standard to revoke a GUID from another organization. The reverse FQDN portion of the original record and the revoking record should match before an entity or organization processing ingested records should consider the revocation valid. The validation schema makes no direct attempt to prevent cross-organization revocation from occurring, but consuming entities or organization are encouraged to account for this prohibition when processing ingested records.

**Referencing:** Using a "references" object, a record issued in this standard can reference any other record(s). This is useful for grouping together samples that should be logically collected together.

This standard has no opinion as to whether or not record references should be contained within a single organization (based on the reverse FQDN portion of the GUID). Entities or organizations consuming records in this format can opt to consider cross-organization referencing valid or invalid based on their own internal policies. 

## Reference Information

### Field Types

When referenced in the Document Field Reference, field types refer to the following data types.

<table>
  <tr>
    <td>Field Type</td>
    <td>Description</td>
  </tr>
  <tr>
    <td>String</td>
    <td>Alphanumeric data, appropriately escaped and encoded for inclusion in a JSON document. </td>
  </tr>
  <tr>
    <td>Enum</td>
    <td>Alphanumeric string data, limited to specific acceptable values. This is functionally analogous to a "picklist" in database or data interface parlance.</td>
  </tr>
  <tr>
    <td>GUID</td>
    <td>A globally unique identification string, compliant with the formatting specified in this document (see “Globally Unique Identification”, above).</td>
  </tr>
  <tr>
    <td>Numeric</td>
    <td>A numeric value, either as an integer or a  floating point number.</td>
  </tr>
  <tr>
    <td>Date</td>
    <td>An RFC 3339 compliant date string, including date, time, and time zone.</td>
  </tr>
  <tr>
    <td>URL</td>
    <td>A String, representing a valid URL including protocol, host, and if applicable, a path and query string.</td>
  </tr>
  <tr>
    <td>Boolean</td>
    <td>A boolean string of either true, or false.</td>
  </tr>
  <tr>
    <td>Object</td>
    <td>A valid JSON object, representing a collection of additional values that are grouped together.</td>
  </tr>
  <tr>
    <td>Array</td>
    <td>A valid JSON array, representing a collection of objects.</td>
  </tr>
</table>


### Object References

#### Main Document Field Reference

<table>
  <tr>
    <td>Field Name</td>
    <td>Type</td>
    <td>Required</td>
    <td>Description</td>
  </tr>
  <tr>
    <td>$schema</td>
    <td>URL</td>
    <td>Yes</td>
    <td>A URL, pointing to a publicly accessible copy of the schema document that is being used for validation. This is typically the global URL provided by Swim Drink Fish Canada.</td>
  </tr>
  <tr>
    <td>documentTime
</td>
    <td>Date</td>
    <td>Yes</td>
    <td>A timestamp indicating when this document was published. This is generally when the document was authored (typically the current time if authored dynamically) and is unrelated to the timestamp that a sample was collected or published.</td>
  </tr>
  <tr>
    <td>records</td>
    <td>Array</td>
    <td>Yes (At least one record)</td>
    <td>An Array of objects, each representing a water quality sample record. At least one record is required. See "Water Quality Sample Object Reference", below.</td>
  </tr>
</table>


#### Water Quality Sample Object Reference

<table>
  <tr>
    <td>Field Name</td>
    <td>Type</td>
    <td>Required</td>
    <td>Description</td>
  </tr>
  <tr>
    <td>guid</td>
    <td>GUID</td>
    <td>Yes</td>
    <td>A globally unique identification for this water quality sample/result. Please refer to the field type reference for GUID elsewhere in this document. </td>
  </tr>
  <tr>
    <td>publicationTime</td>
    <td>Date</td>
    <td>Yes</td>
    <td>A timestamp indicating when this sample result was first published. This timestamp should remain persistent.</td>
  </tr>
  <tr>
    <td>updateTime</td>
    <td>Date</td>
    <td>No</td>
    <td>An optional timestamp indicating when this sample result was last updated. This timestamp can be omitted if the sample result has always remained consistent.</td>
  </tr>
  <tr>
    <td>organizationName</td>
    <td>String</td>
    <td>Yes</td>
    <td>A string indicating the name and any identifying information about the organization who originally published this sample, for example an email address or web site address.</td>
  </tr>
  <tr>
    <td>revokes</td>
    <td>Object</td>
    <td>No</td>
    <td>An object indicating another sample document that is effectively revoked by the existence of this document. Please see the section "Sample Result Revocation and Referencing" above for more information.</td>
  </tr>
  <tr>
    <td>references</td>
    <td>Array</td>
    <td>No</td>
    <td>An array of objects. This allows you to specify other water quality samples that are related to this sample. This is useful for grouping water quality samples together. See “Sample Result Revocation and Referencing” above for more information and“Other Object Reference” below for object reference information.</td>
  </tr>
  <tr>
    <td>advisory</td>
    <td>Object</td>
    <td>Yes</td>
    <td>An object indicating if the described water quality sample is in excess of the prevailing local standard for recreational water use. See “Advisory Object Reference”, below.</td>
  </tr>
  <tr>
    <td>location</td>
    <td>Object</td>
    <td>Yes</td>
    <td>A location object describing the location where this sample was taken. This location is generally accepted, publicly understood location description. Typically, this would be used to indicate a beach, park, or access location and not the exact location where a sample was collected; more precise location can be included with the actual sample data. See “Location Object Reference”, below.</td>
  </tr>
  <tr>
    <td>sample</td>
    <td>Object</td>
    <td>Yes</td>
    <td>An object describing the sample result data. See “Sample Object Reference”, below.</td>
  </tr>
</table>


#### Other Object Reference

<table>
  <tr>
    <td>Field Name</td>
    <td>Type</td>
    <td>Required</td>
    <td>Description</td>
  </tr>
  <tr>
    <td>guid</td>
    <td>GUID</td>
    <td>Yes</td>
    <td>A GUID of another sample record that is being referenced by this one.</td>
  </tr>
</table>


#### Revokes Object Reference

<table>
  <tr>
    <td>Field Name</td>
    <td>Type</td>
    <td>Required</td>
    <td>Description</td>
  </tr>
  <tr>
    <td>guid</td>
    <td>GUID</td>
    <td>Yes</td>
    <td>A GUID of another sample record that is being revoked by this one.</td>
  </tr>
  <tr>
    <td>explanation</td>
    <td>String</td>
    <td>No</td>
    <td>An optional explanation indicating the nature of this revokation. This would typically explain the reason the revokation is being issued.</td>
  </tr>
</table>

#### Advisory Object Reference

<table>
  <tr>
    <td>Field Name</td>
    <td>Type</td>
    <td>Required</td>
    <td>Description</td>
  </tr>
  <tr>
    <td>issued</td>
    <td>Boolean</td>
    <td>Yes</td>
    <td>A boolean indicating if the described water quality sample exceeds the prevailing local standard for recreational water use.</td>
  </tr>
  <tr>
    <td>description</td>
    <td>String</td>
    <td>No</td>
    <td>An optional description indicating the nature of this advisory. This description can also be included if no advisory has been issued in order to clarify a non-advisory state.</td>
  </tr>
</table>


#### Sample Object Reference

<table>
  <tr>
    <td>Field Name</td>
    <td>Type</td>
    <td>Required</td>
    <td>Description</td>
  </tr>
  <tr>
    <td>collectionTime</td>
    <td>Date</td>
    <td>Yes</td>
    <td>A timestamp indicating the exact date and time that this water sample was collected.</td>
  </tr>
  <tr>
    <td>location</td>
    <td>Object</td>
    <td>Yes</td>
    <td>A location object describing the precise location where this sample was collected. See "Location Object Reference", below.</td>
  </tr>
  <tr>
    <td>type</td>
    <td>Object</td>
    <td>Yes</td>
    <td>The type of water sample being described. See “Sample Type Object Reference”, below.</td>
  </tr>
  <tr>
    <td>method</td>
    <td>Enum</td>
    <td>Yes</td>
    <td>A string enumeration value indicating the method code that was used to enumerate the sample result number. This method code should correspond to a method used to enumerate the sampled substance.  For more information about the valid method codes, please see “Appendix 1 - Method Codes”</td>
  </tr>
  <tr>
    <td>substance</td>
    <td>Enum</td>
    <td>Yes</td>
    <td>A string enumeration value indicating the substance that was tested for when determining this sample result. For example, e.coli, enterococci, or cyanobacteria.  For more information about the valid substance codes, please see “Appendix 2 - Substance Codes”.</td>
  </tr>
  <tr>
    <td>units</td>
    <td>Enum</td>
    <td>Yes</td>
    <td>The sample result units that are being expressed. Accepted values include:

cfu - The sample result number is expressed as colony forming units/100mL.
mpn - The sample result number is expressed as most probably number/100 mL.
cce -  The sample result number is expressed in calibrator cell equivalents/100mL
cells - The sample result number is expressed in cells/mL
micro - the sample result number is expressed in µg/L</td>
  </tr>
  <tr>
    <td>result</td>
    <td>Number</td>
    <td>Yes</td>
    <td>The sample result value for this sample, defined as simply a number.</td>
  </tr>
  <tr>
    <td>collector</td>
    <td>String</td>
    <td>No</td>
    <td>An optional string allowing the publisher to indicate any name or contact information for the person who physically collected the sample being described.</td>
  </tr>
</table>


#### Sample Type Object Reference

<table>
  <tr>
    <td>Field Name</td>
    <td>Type</td>
    <td>Required</td>
    <td>Description</td>
  </tr>
  <tr>
    <td>kind</td>
    <td>Enum</td>
    <td>Yes</td>
    <td>The kind of sample that was collected. Acceptable values include:

single - A single / grab sample result.
composite -  A composite sample result.
predicted -  A sample result generated by predictive model.
geomean - A geometric mean sample result.</td>
  </tr>
  <tr>
    <td>variant</td>
    <td>Enum</td>
    <td>Yes*</td>
    <td>The variant of the geometric mean (geomean) sample result being described:

spatial - A spatial geometric mean, comprised of samples distributed over a geographical area.
temporal - A temporal geometric mean, comprised of samples collected over a period of time.</td>
  </tr>
  <tr>
    <td>hours</td>
    <td>Number</td>
    <td>Yes**</td>
    <td>A number (decimals are acceptable) indicating the number of hours between the collection time of the individual samples that comprise this geometric mean result.</td>
  </tr>
</table>


** ***_variant_*** is required if ***_kind_*** is specified as a ***_geomean_*** -** Otherwise, it is omitted.*

*** ***_hours_*** is required if ***_variant_*** is included and specified as ***_temporal_*** -** Otherwise, it is omitted.*

#### Location Object Reference

<table>
  <tr>
    <td>Field Name</td>
    <td>Type</td>
    <td>Required</td>
    <td>Description</td>
  </tr>
  <tr>
    <td>id</td>
    <td>String</td>
    <td>Yes</td>
    <td>An identifier for this location.</td>
  </tr>
  <tr>
    <td>name</td>
    <td>String</td>
    <td>Yes</td>
    <td>A name/description for this location.</td>
  </tr>
  <tr>
    <td>coordinate</td>
    <td>Object</td>
    <td>Yes</td>
    <td>A coordinate object for this location. See "Coordinate Object Reference", below.</td>
  </tr>
</table>


#### Coordinate Object Reference

<table>
  <tr>
    <td>Field Name</td>
    <td>Type</td>
    <td>Required</td>
    <td>Description</td>
  </tr>
  <tr>
    <td>latitude</td>
    <td>Number</td>
    <td>Yes</td>
    <td>The latitude, as a floating point number. Must be a valid number between -90.0 and +90.0</td>
  </tr>
  <tr>
    <td>longitude</td>
    <td>Number</td>
    <td>Yes</td>
    <td>The longitude, as a floating point number. Must be a valid number between -90.0 and +90.0</td>
  </tr>
</table>


## Appendix 1 - Method Codes

<table>
  <tr>
    <td>Method Code</td>
    <td>Type</td>
    <td>Official method name</td>
  </tr>
  <tr>
    <td>600-R-00-013</td>
    <td>Membrane Filtration (CFU)</td>
    <td>Membrane filter method for the simultaneous detection of total coliforms and Escherichia coli in drinking water.</td>
  </tr>
  <tr>
    <td>1103.1</td>
    <td>Membrane Filtration (CFU)</td>
    <td>Test method for Escherichia coli and enterococci in water by the membrane-filter procedure</td>
  </tr>
  <tr>
    <td>1106.1</td>
    <td>Membrane Filtration (CFU)</td>
    <td>Enterococci in Water by Membrane Filtration Using membrane Enterococcus-Esculin Iron Agar (mE-EIA)</td>
  </tr>
  <tr>
    <td>1600</td>
    <td>Membrane Filtration (CFU)</td>
    <td>Enterococci in water by membrane filtration using mEI Agar</td>
  </tr>
  <tr>
    <td>1603</td>
    <td>Membrane Filtration (CFU)</td>
    <td>Escherichia coli (E. coli) in Water by Membrane Filtration Using Modified membrane-Thermotolerant Escherichia coli Agar (Modified mTEC)</td>
  </tr>
  <tr>
    <td>1604</td>
    <td>Membrane Filtration (CFU)</td>
    <td>Total Coliforms and E. coli in Drinking Water by Membrane Filtration</td>
  </tr>
  <tr>
    <td>9213D</td>
    <td>Membrane Filtration (CFU)</td>
    <td>Recreational Waters. 9213D. Natural Bathing Beaches</td>
  </tr>
  <tr>
    <td>10029</td>
    <td>Membrane Filtration (CFU)</td>
    <td>Coliforms: Membrane Filtration (simultaneous detection) In: Hach Analytical Procedures. m-ColiBlue24 Broth Procedure for Membrane Filtration. Hach Product Literature #8433. Hach: Loveland, CO. Dec.</td>
  </tr>
  <tr>
    <td>coliscan_MF</td>
    <td>Membrane Filtration (CFU)</td>
    <td>E. coli and other coliform bacteria by Coliscan MF</td>
  </tr>
  <tr>
    <td>9221A</td>
    <td>Multiple tube fermentation (MPN)</td>
    <td>Multiple-tube Fermentation Technique for Members of the Coliform Group: 9221A. Introduction. 9221B. Standard Total Coliform Fermentation Technique 9221C. Estimation of Bacterial Density 9221F.</td>
  </tr>
  <tr>
    <td>9221B</td>
    <td>Multiple tube fermentation (MPN)</td>
    <td>Multiple-tube Fermentation Technique for Members of the Coliform Group: 9221A. Introduction. 9221B. Standard Total Coliform Fermentation Technique 9221C. Estimation of Bacterial Density 9221F.</td>
  </tr>
  <tr>
    <td>9221C</td>
    <td>Multiple tube fermentation (MPN)</td>
    <td>Multiple-tube Fermentation Technique for Members of the Coliform Group: 9221A. Introduction. 9221B. Standard Total Coliform Fermentation Technique 9221C. Estimation of Bacterial Density 9221F.</td>
  </tr>
  <tr>
    <td>9221F</td>
    <td>Multiple tube fermentation (MPN)</td>
    <td>Multiple-tube Fermentation Technique for Members of the Coliform Group: 9221A. Introduction. 9221B. Standard Total Coliform Fermentation Technique 9221C. Estimation of Bacterial Density 9221F.</td>
  </tr>
  <tr>
    <td>9222B</td>
    <td>Multiple tube fermentation (MPN)</td>
    <td>Membrane Filter Technique for Members of the Coliform Group 9222B. Standard Total Coliform Membrane Filter Procedure. 9222G. MF Partition Procedures 9221F. Escherichia coli Procedure (Proposed)</td>
  </tr>
  <tr>
    <td>9222G</td>
    <td>Multiple tube fermentation (MPN)</td>
    <td>Membrane Filter Technique for Members of the Coliform Group 9222B. Standard Total Coliform Membrane Filter Procedure. 9222G. MF Partition Procedures 9221F. Escherichia coli Procedure (Proposed)</td>
  </tr>
  <tr>
    <td>9223B</td>
    <td>Multiple tube fermentation (MPN)</td>
    <td>Enzyme Substrate Coliform Test: 9223B Enzyme Substrate Test</td>
  </tr>
  <tr>
    <td>9223B_colilert</td>
    <td>Multiple tube fermentation (MPN)</td>
    <td>Enzyme Substrate Coliform Test: 9223B Enzyme Substrate Test using Idexx Colilert</td>
  </tr>
  <tr>
    <td>9223B_colilert18</td>
    <td>Multiple tube fermentation (MPN)</td>
    <td>Enzyme Substrate Coliform Test: 9223B Enzyme Substrate Test using Idexx Colilert-18</td>
  </tr>
  <tr>
    <td>1680</td>
    <td>Multiple tube fermentation (MPN)</td>
    <td>Fecal Coliforms in Sewage Sludge (Biosolids) by Multiple-Tube Fermentation using Lauryl Tryptose Broth (LTB) and EC Medium</td>
  </tr>
  <tr>
    <td>1681</td>
    <td>Multiple tube fermentation (MPN)</td>
    <td>Fecal Coliforms in Sewage Sludge (Biosolids) by Multiple-Tube Fermentation using A-1 medium</td>
  </tr>
  <tr>
    <td>1611.1</td>
    <td>qPCR</td>
    <td>Enterococci in Water by
TaqMan® Quantitative Polymerase Chain
Reaction (qPCR)</td>
  </tr>
  <tr>
    <td>1609.1</td>
    <td>qPCR</td>
    <td>Enterococci in Water
by TaqMan® Quantitative
Polymerase Chain Reaction (qPCR)
with Internal Amplification Control
(IAC) Assay</td>
  </tr>
  <tr>
    <td>BioGX-ecoli</td>
    <td>qPCR</td>
    <td>E.coli Species, no IAC. BioGX Product #201-0025
Quantitative analysis</td>
  </tr>
  <tr>
    <td>Method C</td>
    <td>qPCR</td>
    <td>EPA Draft Method C : Escherichia coli (e.coli) in Water by TaqMan Quantitative
Polymerase Chain Reaction (qPCR) </td>
  </tr>
  <tr>
    <td>easygel</td>
    <td>Direct Inoculation (cfu)</td>
    <td>Detection of waterborne coliforms and E. coli with Coliscan Easygel</td>
  </tr>
  <tr>
    <td>petrifilm</td>
    <td>Direct Inoculation (cfu)</td>
    <td></td>
  </tr>
  <tr>
    <td>544</td>
    <td>Cyanobacteria and toxins detection</td>
    <td>DETERMINATION OF MICROCYSTINS AND NODULARIN IN DRINKING WATER BY SOLID PHASE EXTRACTION AND LIQUID CHROMATOGRAPHY/TANDEM MASS SPECTROMETRY (LC/MS/MS)</td>
  </tr>
  <tr>
    <td>545</td>
    <td>Cyanobacteria and toxins detection</td>
    <td>Determination of Cylindrospermopsin and
Anatoxin-a in Drinking Water by Liquid Chromatography
Electrospray Ionization Tandem Mass Spectrometry
(LC/ESI-MS/MS)</td>
  </tr>
  <tr>
    <td>546</td>
    <td>Cyanobacteria and toxins detection</td>
    <td>Determination of Total Microcystins and Nodularins in Drinking Water and Ambient Water by Adda Enzyme-Linked Immunosorbent Assay</td>
  </tr>
  <tr>
    <td>aquatox</td>
    <td>Predictive / Statistical models</td>
    <td>AQUATOX</td>
  </tr>
  <tr>
    <td>BASINS</td>
    <td>Predictive / Statistical models</td>
    <td>Better Assessment Science Integrating point & Non-point Sources (BASINS)</td>
  </tr>
  <tr>
    <td>cormix</td>
    <td>Predictive / Statistical models</td>
    <td>Cornell Mixing Zone Expert System (Cormix)</td>
  </tr>
  <tr>
    <td>DMR</td>
    <td>Predictive / Statistical models</td>
    <td>DMR Pollutant Loading Tool: The Discharge Monitoring Report (DMR) Pollutant Loading Tool</td>
  </tr>
  <tr>
    <td>EnDDaT</td>
    <td>Predictive / Statistical models</td>
    <td>Environmental Data Discovery and Transformation (EnDDaT) service</td>
  </tr>
  <tr>
    <td>swimCast</td>
    <td>Predictive / Statistical models</td>
    <td>SwimCast</td>
  </tr>
  <tr>
    <td>nowCast</td>
    <td>Predictive / Statistical models</td>
    <td>NowCast</td>
  </tr>
  <tr>
    <td>virtualBeach</td>
    <td>Predictive / Statistical models</td>
    <td>Virtual Beach</td>
  </tr>
  <tr>
    <td>wasp7</td>
    <td>Predictive / Statistical models</td>
    <td>Water Quality Analysis Simulation Program (WASP)</td>
  </tr>
</table>


## Appendix 2 - Substance Codes

<table>
  <tr>
    <td>Substance Name</td>
    <td>Substance Type</td>
  </tr>
  <tr>
    <td>total_coliform</td>
    <td>Fecal Indicator Bacteria</td>
  </tr>
  <tr>
    <td>fecal_coliform</td>
    <td>Fecal Indicator Bacteria</td>
  </tr>
  <tr>
    <td>ecoli</td>
    <td>Fecal Indicator Bacteria</td>
  </tr>
  <tr>
    <td>enterococcus</td>
    <td>Fecal Indicator Bacteria</td>
  </tr>
  <tr>
    <td>cyanobacteria</td>
    <td>Cyanobacteria / Blue Green Algae</td>
  </tr>
  <tr>
    <td>microcystin</td>
    <td>Cyanotoxin</td>
  </tr>
  <tr>
    <td>cylindrospermopsin</td>
    <td>Cyanotoxin</td>
  </tr>
</table>



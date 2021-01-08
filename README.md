# Transparency in Coverage
The technical implementation guide for the machine readable files as required by the [Transparency in Coverage](https://www.federalregister.gov/documents/2019/11/27/2019-25011/transparency-in-coverag) final rules (85 FR 72158).

Overview
========

This repository contains a set of schemas describing a data format (example implementations are encoded as JSON and XML) for the Transparency in Coverage final rule.
All machine-readable files must conform to a non-proprietary, open standards format that is platform independent and made available to the public without restrictions that would impede the re-use of that information.

Background
==========

The Departments of the Treasury, Labor, and Health and Human Services (the Departments) have issued the Transparency in Coverage final rules (85 FR 72158) on November 12, 2020. The final rules require non-grandfathered group health plans and health insurance issuers in the individual and group markets (plans and issuers) to disclose certain pricing information. Under the final rules a plan or issuer must disclose in-network negotiated rates, billed and out-of-network allowed, and in-network drug prices (including both “negotiated rates” and “historical net prices”) through three machine-readable files posted on an internet website.  

Plans and issuers are required to make these files public for plan or policy years beginning on or after January 1, 2022.

Developer Documentation
=======================

Transport mechanism - HTTPS
--------------------------

All machine-readable files must be made available via HTTPS.

Content type - Non-Proprietary, Open Format
-------------------

There are plenty of great formats to work with that will meet the needs for Transparency in Coverage:
* [JSON](https://www.json.org/)
* [XML](http://www.xml.org/)
* [YAML](https://yaml.org/)

Examples of proprietary formats that do *not* meet this definition would be:
* [PDF](https://en.wikipedia.org/wiki/PDF)
* [XLS/XLSX](https://en.wikipedia.org/wiki/Microsoft_Excel#File_formats) 

Public Discoverability
----------------------

These machine-readable files post made available to the public without restrictions that would impede the re-use of that information.

The location of the these URLs *must* be provided over HTTPS to ensure the integrity of the data.

Special Data Types
------------------

Dates should be strings in [ISO 8601 format](https://en.wikipedia.org/wiki/ISO_8601) (e.g. YYYY-MM-DD).

Different Flat Files
--------------------

There three required flat files associated with Transparency in Coverage:
* In-Network Negotiated Rates
* Out-Of-Network Allowed Amounts
* In-Network Prescription Drugs

**In-Network Negotiated Rates File**
Under the finalized rules, a plan or issuer must disclose in-network provider negotiated rates for all items and services through a machine-readable file.

**Out-Of-Network Allowed Amounts File**
Under the finalized rules, a plan or issuer must disclose certain data elements to the public, including the billed and allowed amounts for out-of-network providers, through a machine-readable file.

**In-Network Prescription Drugs File**
Under the finalized rules, a plan or issuer must disclose in-network prescription drug's historical net price and negotiated rate through a machine-readable file.

The associated names for those files are:
* `in-network-rates`
* `allowed-amounts`
* `prescription-drugs`

File Naming Convention  
-------------------------
The following is the required naming standard for each file: `<YYYY-MM-DD>_<payer or issuer name>_<plan name>_<file type name>.json`

For example, the following would be the required naming for CMS:
* `2020-01-05_cms_medicare_negotiated-rates.json`
* `2020-01-05_cms_medicare_allowed-amounts.json`
* `2020-01-05_cms_medicare_prescription-drugs.json`

Schemas
=======

### In-Network File

| Field | Name | Type | Definition | Required |
| ----- | ---- | ---- | ---------- | -------- |
| **reporting_entity_name** | Entity Name | String | The legal name of the entity publishing the machine-readable file. | Yes |
| **reporting_entity_type** | Entity Type | String | The type of entity that is publishing the machine-readable file (a group health plan, health insurance issuer, or a third party with which the plan or issuer has contracted to provide the required information, such as a third-party administrator, a health care claims clearinghouse, or a health insurance issuer that has contracted with a group health plan sponsor). | Yes |
| **plan_name** | Plan Name | String | The plan name and name of plan sponsor and/or insurance company. | Yes |
| **plan_id_type** | Plan Id Type | String | Allowed values: "EIN" and "HIOS" | No |
| **plan_id** | Plan ID | String | The 14-digit Health Insurance Oversight System (HIOS) identifier, or, if the 14-digit HIOS identifier is not available, the 5-digit HIOS identifier, or if no HIOS identifier is available, the Employer Identification Number (EIN)for each plan or coverage offered by a plan or issuer. | No |
| **plan_market_type** | Market Type | String | Allowed values: "group" and "individual" | Yes |
| **in_network** | In-Network | Array | An array of [in-network object types](#in-network-object) | Yes |
| **last_updated_on** | Last Updated On | String | The date in which the file was last updated. Date must be in an ISO 8601 format (e.g. YYYY-MM-DD) | Yes |

#### In-Network Object

This type defines an in-network object.

| Field | Name | Type | Definition | Required |
| ----- | ---- | ---- | ---------- | -------- |
| **payment_type** | Payment Type | String | An indication as to whether a reimbursement arrangement other than a standard fee-for-service model applies. Allowed values: "bundle" or "capitation"| No |
| **name** | Name | String | This is name of the item/service that is offered | Yes |
| **billing_code_type** | Billing Code Type | String | Allowed values: "CPT", "HCPCS", "ICD", and "DRG" | Yes |
| **billing_code_type_version** | Billing Code Type Version | String | There might be versions associated with the `billing_code_type`. For example, Medicare is currently using ICD's version 10 | Yes |
| **billing_code** | Billing Code | String | The code used by a plan or issuer or its in-network providers to identify health care items or services for purposes of billing, adjudicating, and paying claims for a covered item or service. | Yes |
| **description** | Description | String | Brief description of the item/service | No |
| **negotiated_rates** | Negotiated Rates | Array | This is an array of [negotiated rate details object types](#negotiated-rate-details-object) | Yes |

#### Negotiated Rate Details Object

This type defines a negotiated rate object.

| Field | Name | Type | Definition | Required |
| ----- | ---- | ---- | ---------- | -------- |
| **negotiated_rate** | Negotiated Rate |	Object | The [negotiated rate object](#negotiated-rate-object) defines information about the type of negotiated rate as well as the dollar amount of the negotiated rate | Yes |
| **providers** | Providers | Array | An array of provider identification numbers (NPI). This can be a list of type 1 and type 2 NPIs | Yes |
| **tin** | Tax Identification Number | String | The unique identification number issued either by the Social Security Administration or by the Internal Revenue Service (IRS). | Yes |
| **service_code** | Place of Service Code | String | The [CMS-maintained two-digit code](https://www.cms.gov/Medicare/Coding/place-of-service-codes/Place_of_Service_Code_Set) that is placed on a professional claim to indicate the setting in which a service was provided | Yes |

#### Negotiated Rate Object
| Field | Name | Type | Definition | Required |
| ----- | ---- | ---- | ---------- | -------- |
| **negotiated_type** | Negotiated Type |	String | There are a few ways in which negotiated rates can happen. Allowed values: "Negotiated", "Derived", and "Fee Schedule"| Yes |
| **negotiated_price** | Negotiated Price | Number | The negotiated dollar amount based on the `negotiation_type` | Yes |
| **expiration_date** | Expiration Date | String | The date in which the agreement for the `negotiated_price` based on the `negotiated_type` ends. Date must be in an ISO 8601 format (e.g. YYYY-MM-DD) | Yes |


### Out-Of-Network Allowed Amount File

| Field | Name | Type | Definition | Required |
| ----- | ---- | ---- | ---------- | -------- |
| **plan_name** | Plan Name | String | The name of the plan of which these prices are set for the given items and services | Yes |
| **plan_id_type** | Plan Id Type | String | Allowed values: "EIN" and "HIOS" | No |
| **plan_id** | Plan ID | String | If available, this will be either the plan's [EIN](https://en.wikipedia.org/wiki/Employer_Identification_Number) or the 14-character, HIOS-generated Plan ID number. (Plan IDs must be unique, even across different markets.) | No |
| **market_type** | Market Type | String | Allowed values: "group" and "individual" | Yes |
| **out_of_network** | Out-Of-Network | Array | An array of [out-of-network object types](#out-of-network-object) | Yes |
| **last_updated_on** | Last Updated On | String | The date in which the file was last updated. Date must be in an ISO 8601 format (e.g. YYYY-MM-DD) | Yes |

#### Out-Of-Network Object

| Field | Name | Type | Definition | Required |
| ----- | ---- | ---- | ---------- | -------- |
| **type** | Type | String | Identifier of an item or service. Allowed values are: "item" and "service" | Yes |
| **name** | Name | String | This is the name of either the item or service | Yes |
| **billing_code_type** | Billing Code Type | String | Allowed values: "CPT", "HCPCS", "ICD", and "DRG" | Yes |
| **billing_code** | Billing Code | String | The code for the item/service | Yes |
| **billing_code_type_version** | Billing Code Type Version | String | There might be versions associated with the `billing_code_type`. For example, Medicare is currently using ICD's version 10 | Yes |
| **description** | Description | String | Brief description of the item/service | No |
| **payments** | Rates |	Array | An array of [out-of-network payments objects](#out-of-network-payment-object) | Yes |

#### Out-Of-Network Payment Object
| Field | Name | Type | Definition | Required |
| ----- | ---- | ---- | ---------- | -------- |
| **allowed_amount** | Allowed Amount | Number | The actual amount paid to the provider for the service performed after all deductions and calculations are performed. This does not include the amount paid fee for service on a capitated service. | Yes |
| **npi** | National Provider Index | Array | An array of provider identification numbers (NPI) | Yes |

### Rx file schema

This schema describes the Rx attributes that are necessary for the drug flat file.

| Field | Name | Type | Definition | Required |
| ----- | ---- | ---- | ---------- | -------- |
| **reporting_entity_name** | Entity Name | String | The legal name of the entity publishing the machine-readable file. | Yes |
| **reporting_entity_type** | Entity Type | String | The type of entity that is publishing the machine-readable file (a group health plan, health insurance issuer, or a third party with which the plan or issuer has contracted to provide the required information, such as a third-party administrator, a health care claims clearinghouse, or a health insurance issuer that has contracted with a group health plan sponsor). | Yes |
| **plan_name** | Plan Name | String | The plan name and name of plan sponsor and/or insurance company (for example, “Maximum Health Plan: Alpha Insurance Group”). | Yes |
| **plan_id_type** | Plan ID Type | String | Allowed values: "EIN" and "HIOS" | Yes |
| **plan_id** | Plan ID | String | The 14-digit Health Insurance Oversight System (HIOS) identifier, or, if the 14-digit HIOS identifier is not available, the 5-digit HIOS identifier, or if no HIOS identifier is available, the Employer Identification Number (EIN), for each coverage option offered by a plan or issuer | Yes |
| **market_type** | Market Type | String | Allowed values: "group" and "individual" | Yes |
| **drugs** | Drugs |	Array | An array of [drug information objects](#drug-object) | Yes |
| **last_updated_on** | Last Updated On | String | The date in which the file was last updated. Date must be in an ISO 8601 format (e.g. YYYY-MM-DD) | Yes |

#### Drug Object

This type defines a drug object

| Field | Name | Type | Definition | Required |
| ----- | ---- | ---- | ---------- | -------- |
| **drug_name** | Drug Name |	String | The proprietary and nonproprietary name assigned to the National Drug Code (NDC) by the Food and Drug Administration (FDA). | Yes |
| **drug_type** | Drug Type |	String | Allowed values: "branded", "generic", or "biosimilar" | Yes |
| **ndc** | National Drug Code | String | A unique 10-digit or 11-digit, 3-segment number assigned by the FDA, which provides a universal product identifier for drugs in the United States.  Data reporting will be on the **first 8 digits** of the full 10-digit or 11-digit NDCs. The last 2 digits of the full 10-digit or 11-digit NDC specify quantity and do not have an impact on the negotiated rate or historic net price. | Yes |
| **prices** | Prices | Array | An array of [drug price objects](#drug-price-object) | Yes |

#### Drug Price Object

This type defines a drug price object

| Field | Name | Type | Definition | Required |
| ----- | ---- | ---- | ---------- | -------- |
| **historical_net_price** | Historical Net Price |	Number | The retrospective average amount paid, reflected as a dollar amount, by a plan or issuer to an in-network provider for the 90-day period beginning 180 days before the file publication date, including any in-network pharmacy or other prescription drug dispenser, for a prescription drug, inclusive of any reasonably allocated rebates, discounts, chargebacks, fees, and any additional price concessions received by the plan or issuer with respect to the prescription drug or prescription drug service.  The historic net price must be reported at the billing unit level as defined by the NCPDP. The standard contains three units. Each “EA,” Milliliter “ML,” or Gram “GM.” | Yes |
| **negotiated_rate** | Negotiated Rate |	Number | The amount, reflected as a dollar amount, that a plan or issuer has contractually agreed to pay an in-network provider, including an in-network pharmacy or other prescription drug dispenser, whether directly or indirectly, including through a third-party administrator or pharmacy benefit manager, for prescription drugs. The negotiated rate must be reported at the billing unit level as defined by NCPDP. The standard contains three units “EA,” “ML,” or “GM.” Fees that are assessed at the point of sale must be reflected separately as a dollar amount (see Dispensing Fee, Administrative Fee, and Transaction Fee data elements). | Yes |
| **administration_fee** | Administration Fee | Number | The fee, reflected as a dollar amount, charged by the Pharmacy Benefit Manager to the plan or issuer for administrating each prescription. This fee must be reflected separately only for the negotiated rate data element. | Yes |
| **dispensing_fee** | Dispensing Fee | Number | The fee, reflected as a dollar amount, for dispensing a prescription applied at the point of sale. This fee must be reflected separately only for the negotiated rate data element. | Yes |
| **transaction_fee** | Transaction Fee |	Number | Any fees, reflected as a dollar amount, assessed when processing a prescription that is not associated with the administrative or dispensing fee. This fee must be reflected separately only for the negotiated rate data element. | Yes |
| **tin** | Tax Identification Number | String | The unique identification number issued either by the Social Security Administration or by the Internal Revenue Service (IRS). | Yes |
| **service_code** | Place of Service Code | String | The [CMS-maintained two-digit code](https://www.cms.gov/Medicare/Coding/place-of-service-codes/Place_of_Service_Code_Set) that is placed on a professional claim to indicate the setting in which a service was provided | Yes |
| **pharmacies** | Negotiated Rate |	Array | Pharmacy Array Object | Yes |


### Examples
* [In-Network Sample](https://github.com/CMSgov/price-transparency-guide/blob/master/examples/in-network-sample.json)
* [Out-of-Network Sample](https://github.com/CMSgov/price-transparency-guide/blob/master/examples/out-of-network-sample.json)
* [Prescription Drug Sample](https://github.com/CMSgov/price-transparency-guide/blob/master/examples/out-of-network-sample.json)

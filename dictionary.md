# Data Dictionary

## opportunities_q3_2026

SAM.gov notices (pre-award pipeline + SAM award notices). One row per notice.

| Column | Type | Description |
|---|---|---|
| notice_id | string | SAM.gov unique notice ID (primary key) |
| title | string | Notice title |
| solicitation_number | string | Solicitation / reference number (joins to awards.solicitation_identifier) |
| type | string | Notice type (Solicitation, Presolicitation, Combined Synopsis/Solicitation, Sources Sought, Award Notice, Justification, Special Notice, …) |
| base_type | string | Original notice type before modification |
| naics_code / naics_description | string | Primary NAICS code (and label for common codes) |
| classification_code | string | PSC/FSC product-service code |
| date | date | Posted date (partition key) |
| posted_date / posted_month | string | Posted timestamp / YYYY-MM |
| response_deadline | string | Offer due date (mixed formats, as published) |
| archive_date | string | Date notice archives off SAM.gov |
| set_aside_code / set_aside_description | string | Set-aside type (SBA, SDVOSB, 8(a), HUBZone, WOSB, …) |
| organization_type | string | Office type code |
| active | bool | Notice currently active |
| agency / sub_agency / office | string | Contracting agency hierarchy |
| office_city / office_state / office_zip | string | Contracting office location |
| pop_city / pop_state / pop_zip / pop_country | string | Place of performance |
| award_amount / award_number / award_date / awardee_name | number/string | Award details (Award Notices only) |
| primary_contact_name / email / phone | string | Contracting POC **as published in the notice** |
| description | string | Full notice description text (empty on some award notices) |
| url | string | SAM.gov notice URL |
| source | string | `bulk_csv` (nightly extract) or `api` |

## awards_q3_2026

USAspending prime award summaries. One row per award (deduplicated by
`contract_award_unique_key`, latest action state wins).

| Column | Type | Description |
|---|---|---|
| contract_award_unique_key | string | Award unique key (primary key) |
| award_id_piid | string | Contract/award number (PIID) |
| parent_award_id_piid / parent_award_agency_name / parent_award_type | string | Parent IDV/IDIQ vehicle (task orders roll up here) |
| solicitation_identifier | string | Solicitation number (joins to opportunities.solicitation_number) |
| award_type / type_of_contract_pricing | string | Definitive contract / delivery order / BPA call; FFP, T&M, … |
| extent_competed / type_of_set_aside | string | Competition extent; set-aside type |
| base_action_date / latest_action_date | date | Award start / most recent modification |
| pop_start_date / pop_current_end_date / pop_potential_end_date | date | Period of performance — **recompete timeline** |
| ordering_period_end_date | date | Vehicle ordering deadline |
| obligated_amount | number | Cumulative obligations at latest action ($) |
| current_total_value / potential_total_value | number | Current ceiling / potential value with all options ($) |
| awarding_agency_name / awarding_sub_agency_name / awarding_office_name | string | Buyer hierarchy |
| funding_agency_name / funding_sub_agency_name | string | Who pays (can differ from awarding office) |
| recipient_uei / recipient_name | string | Vendor key (joins to vendors.uei) + name as recorded |
| recipient_parent_uei / recipient_parent_name | string | Corporate parent (for family rollups) |
| recipient_country_name / recipient_state_code / recipient_state_name | string | Vendor location |
| naics_code / naics_description | string | Work classification |
| product_or_service_code / product_or_service_code_description | string | PSC + label |
| award_month | string | YYYY-MM of latest action (partition key) |

## awards_enriched_q3_2026

All `awards_q3_2026` columns, plus (left-joined on `recipient_uei = vendors.uei`):

| Column | Description |
|---|---|
| vendor_legal_name / vendor_dba_name | Canonical registry name |
| vendor_business_types / vendor_sba_business_types | Socio-economic codes (decode: 2X=small business, XS=SDVOSB, 8W=8(a), HQ=HUBZone, MF=minority-owned, A8=nonprofit, LJ=large, …) |
| vendor_entity_structure | Legal structure code |
| vendor_primary_naics | Vendor's self-certified primary NAICS |
| vendor_country_of_incorporation | Domicile |
| vendor_registration_expiration | SAM registration expiry (lapse risk) |
| vendor_exclusion_flag | Debarment/exclusion flag |
| vendor_snapshot_month | Registry snapshot used (2026-09) |

## vendors_snapshot_2026-09

Full SAM entity registry (public tier). One row per registered entity.

`uei` (PK), `cage_code`, `registration_purpose`, `initial_registration_date`,
`registration_expiration_date`, `last_update_date`, `activation_date`,
`legal_name`, `dba_name`, address fields (`address_line1/2`, `city`, `state`,
`zip_code`, `country`, `congressional_district`), `entity_start_date`,
`entity_url`, `entity_structure`, `state_of_incorporation`,
`country_of_incorporation`, `business_types`, `primary_naics`, `naics_codes`,
`psc_codes`, `exclusion_status_flag`, `sba_business_types`,
`no_public_display_flag`, `evs_source`, `snapshot_month`.

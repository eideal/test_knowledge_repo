---
title: Useful SQL
authors:
- Emma Ideal
- Christina Huang
- Lorraine Fan
tags:
- sql
- oracle
created_at: 2017-02-16 00:00:00
updated_at: 2017-02-16 08:37:23.520094
tldr: Handy SQL queries, including useful join predicates and queries we use all the
  time.
---
* Please add to this doc!
* Use the --update command when adding your content to the repository, so you won't create a new knowledge post


**Contents**

[TOC]


### Recruiting Tables

- Helpful join predicates for recruiting tables

```sql
SELECT c.postal_code, f.* 
FROM sechrprod.f_dbrct_app f, sechrprod.stg_src_trex_candidate s, sechrprod.stg_src_candidate_education e, sechrprod.d_candidate c 
WHERE e.candidateid = s.FBID 
AND s.salesforceid = f."Candidate SFID" 
AND f."Candidate SFID" = c.original_id
```

- Offers table

```sql
select
o."Offer SFID", o.candidate_intern_id, o.eid,
-- offer status
o."Current Offer Status", o."Current Offer Status Group",
-- requisition information
o."Current Tech Recr Dept", o."Current Tech Recr Sub Dept", o."Job Level",
-- funnel dates
to_date(to_char(o."App Create Date")) as "App Create Date", -- format to remove time
o."Screening Interview Date", o."Full Interview Loop Date",
o."Packet Review Date", o."Offer Extended Date", o."Offer Decision Date",
o."Current Expected Start Date",
-- days to fill variables
trunc(o."Offer Extended Date" - o."App Create Date") as "Create to Offer Extend",
trunc(o."Current Expected Start Date" - o."App Create Date") as "Create to Start",
trunc(o."Offer Extended Date" - o."Screening Interview Date") as "Screen to Offer Extend",
trunc(o."Full Interview Loop Date" - o."Screening Interview Date") as "Screen to Onsite",
trunc(o."Offer Extended Date" - o."Full Interview Loop Date") as "Onsite to Offer Extend",
from sechrprod.f_dbrct_latest_offer o
where o."Level Num" < 10 -- remove VPs
and o."Current Tech Recr Dept" in ('SWE')
and o."Hire Type Group" = 'FTE'
and o."Exp/UR" = 'Experienced'
```

### PSC Tables

- For promotions data, you can use this nicely formatted table:
```sql
SELECT
p.employee_id, e.hire_date, d.employee_level, p.psc_review_yr, p.psc_review_qtr,
p.psc_cycle_start_dt, p.psc_cycle_end_dt, p.prev_cycle_rating_num, p.this_cycle_rating_num,
p.prev_cycle_promoted_flag, p.this_cycle_promotion_flag, p.total_psc_cycles,
p.current_total_psc_cycles, p.total_promotions, p.current_total_promotions, p.prev_promoted_period
FROM sechrprod.f_dbhr_psc p
LEFT JOIN sechrprod.f_employee e on p.employee_id = e.employee_id
LEFT JOIN sechrprod.f_employee_daily d
ON (d.employee_id = e.employee_id and d.as_of_date = p.psc_cycle_end_dt)
WHERE e.hire_date > '01-JAN-11'
AND p.current_total_psc_cycles > 0
ORDER BY employee_id, psc_review_yr, psc_review_qtr
```

### Search for table or field in Oracle

- Find a table:
```sql
SELECT * 
FROM all_tab_cols 
WHERE table_name LIKE '%ENTELO%'; --use uppercase
```

- Find tables where a field name exists:
```sql
SELECT *
FROM all_tab_cols
WHERE column_name LIKE '%COST_CENTER%'; --use uppercase
```

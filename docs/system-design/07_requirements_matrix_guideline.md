# Chapter 7: Requirements Matrix — Guideline (System Documentation)

This chapter provides a cross-reference that traces components and data structures back to the requirements in Chapter 2 (the SRS).

> Remove any italic notes/instructions from your final submission.

Use a table to show which system components (sequence diagram vs. class) satisfy each functional requirement (use case). Refer to the functional requirements by the codes given to each use case in Chapter 2.

Each row identifies a `Package ID, Use Case ID, Sequence Diagram ID` combination, and an `X` marks each class (entity) involved in that scenario.

**Table 7.1: Requirements Matrix (Use Case/Sequence Diagram vs. Classes)**

|   | Customer | Order | Catalog | CatalogProduct | ProductItem | OrderItem | ReturnItem | InventoryItem | … |
| :---- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- |
| P001, UC001, SD001 | X | X |  |  |  |  |  |  |  |
| P001, UC001, SD002 |  | X |  |  |  |  |  |  |  |
| P002, UC002, SD003 |  |  | X | X |  |  |  |  |  |
| P003, UC003, SD004 |  |  |  |  | X | X | X | X |  |
| P004, UC004, SD005 |  | X |  |  |  |  |  | X |  |
| … |  |  |  |  |  |  |  |  |  |

(Replace the column headers with your own classes/entities, and add a row for each Package/Use Case/Sequence Diagram combination.)

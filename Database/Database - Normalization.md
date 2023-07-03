---
tags: Normalization
---

# Definition

### Transitive dependency
> (A → B) **AND** (B → C) ⇒ (A → C)

**Normalization** is used to **==minimize redundancy==** and dependency by organizing fields and table of a database.
- First normal form (1NF)
- Second normal form (2NF)
	- Must be in 1NF
	- xuất hiện khi **non-key depends partially on candidate keys**
- Third normal form(3NF)
	- Must be in 2NF
	- Don’t have a transitive dependency
- Boyce-Codd normal form(BCNF)

### Detail example:
![[normalize-violated-3NF.png]]

`{Std_Id, subject_Id}`: Compositive key
It has violated **3NF** because there’s a transitive dependency

`(Std_Id, Subject_Id)` -> `Exam_type`
`Exam_type -> Toltal_Mark`
—> Resolve 3NF
![[normalize-resolved-3NF.png]]

Assuming `Toltal_Mark` depends on `(Std_Id, Subject_Id)` ->No need to normalize
Assuming `Toltal_Mark` depends on `(Std_Id)` -> It has violated **2NF**
---
title: Inhabited subtypes
---

```agda
module foundation.inhabited-subtypes where

open import foundation.dependent-pair-types
open import foundation.identity-types
open import foundation.inhabited-types
open import foundation.propositions
open import foundation.subtypes
open import foundation.universe-levels
```

## Idea

An inhabited subtype of a type `A` is a subtype `P` of `A` such that the underlying type of `P` is inhabited.

```agda
is-inhabited-subtype-Prop :
  {l1 l2 : Level} {A : UU l1} → subtype l2 A → UU-Prop (l1 ⊔ l2)
is-inhabited-subtype-Prop P = is-inhabited-Prop (type-subtype P)

is-inhabited-subtype :
  {l1 l2 : Level} {A : UU l1} → subtype l2 A → UU (l1 ⊔ l2)
is-inhabited-subtype P = type-Prop (is-inhabited-subtype-Prop P)

inhabited-subtype :
  {l1 : Level} (l2 : Level) → UU l1 → UU (l1 ⊔ lsuc l2)
inhabited-subtype l2 A = type-subtype (is-inhabited-subtype-Prop {l2 = l2} {A})

module _
  {l1 l2 : Level} {A : UU l1} (P : inhabited-subtype l2 A)
  where

  subtype-inhabited-subtype : subtype l2 A
  subtype-inhabited-subtype = pr1 P

  is-inhabited-subtype-inhabited-subtype :
    is-inhabited-subtype subtype-inhabited-subtype
  is-inhabited-subtype-inhabited-subtype = pr2 P

  type-inhabited-subtype : UU (l1 ⊔ l2)
  type-inhabited-subtype = type-subtype subtype-inhabited-subtype

  inhabited-type-inhabited-subtype : Inhabited-Type (l1 ⊔ l2)
  pr1 inhabited-type-inhabited-subtype = type-inhabited-subtype
  pr2 inhabited-type-inhabited-subtype =
    is-inhabited-subtype-inhabited-subtype

  is-in-inhabited-subtype : A → UU l2
  is-in-inhabited-subtype = is-in-subtype subtype-inhabited-subtype

  is-prop-is-in-inhabited-subtype :
    (x : A) → is-prop (is-in-inhabited-subtype x)
  is-prop-is-in-inhabited-subtype =
    is-prop-is-in-subtype subtype-inhabited-subtype

  inclusion-inhabited-subtype : type-inhabited-subtype → A
  inclusion-inhabited-subtype = inclusion-subtype subtype-inhabited-subtype

  ap-inclusion-inhabited-subtype :
    (x y : type-inhabited-subtype) →
    x ＝ y → (inclusion-inhabited-subtype x ＝ inclusion-inhabited-subtype y)
  ap-inclusion-inhabited-subtype =
    ap-inclusion-subtype subtype-inhabited-subtype

  is-in-inhabited-subtype-inclusion-inhabited-subtype :
    (x : type-inhabited-subtype) →
    is-in-inhabited-subtype (inclusion-inhabited-subtype x)
  is-in-inhabited-subtype-inclusion-inhabited-subtype =
    is-in-subtype-inclusion-subtype subtype-inhabited-subtype
```
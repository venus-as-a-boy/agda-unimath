# Identity types

```agda
{-# OPTIONS --without-K --exact-split --safe #-}

module foundation.identity-types where

open import foundation-core.identity-types public

open import foundation-core.equivalences using
  ( is-equiv; is-equiv-has-inverse; _≃_; _∘e_; is-equiv-id; is-equiv-comp')
open import foundation-core.functions using (_∘_; id)
open import foundation-core.homotopies using (_~_)
open import foundation.dependent-pair-types using (Σ; pair; pr1; pr2)
open import foundation.universe-levels using (UU; Level)
```

## Idea

The equality relation on a type is a reflexive relation, with the universal property that it maps uniquely into any other reflexive relation. In type theory, we introduce the identity type as an inductive family of types, where the induction principle can be understood as expressing that the identity type is the least reflexive relation.

## Properties

### The groupoidal operations on identity types are equivalences

```agda
module _
  {l : Level} {A : UU l}
  where
  
  abstract
    is-equiv-inv : (x y : A) → is-equiv (λ (p : Id x y) → inv p)
    is-equiv-inv x y = is-equiv-has-inverse inv inv-inv inv-inv

  equiv-inv : (x y : A) → (Id x y) ≃ (Id y x)
  pr1 (equiv-inv x y) = inv
  pr2 (equiv-inv x y) = is-equiv-inv x y

  inv-concat : {x y : A} (p : Id x y) (z : A) → Id x z → Id y z
  inv-concat p = concat (inv p)

  isretr-inv-concat :
    {x y : A} (p : Id x y) (z : A) → (inv-concat p z ∘ concat p z) ~ id
  isretr-inv-concat refl z q = refl

  issec-inv-concat :
    {x y : A} (p : Id x y) (z : A) → (concat p z ∘ inv-concat p z) ~ id
  issec-inv-concat refl z refl = refl

  abstract
    is-equiv-concat :
      {x y : A} (p : Id x y) (z : A) → is-equiv (concat p z)
    is-equiv-concat p z =
      is-equiv-has-inverse
        ( inv-concat p z)
        ( issec-inv-concat p z)
        ( isretr-inv-concat p z)

  equiv-concat :
    {x y : A} (p : Id x y) (z : A) → Id y z ≃ Id x z
  pr1 (equiv-concat p z) = concat p z
  pr2 (equiv-concat p z) = is-equiv-concat p z
  
  inv-concat' : (x : A) {y z : A} → Id y z → Id x z → Id x y
  inv-concat' x q = concat' x (inv q)

  isretr-inv-concat' :
    (x : A) {y z : A} (q : Id y z) → (inv-concat' x q ∘ concat' x q) ~ id
  isretr-inv-concat' x refl refl = refl

  issec-inv-concat' :
    (x : A) {y z : A} (q : Id y z) → (concat' x q ∘ inv-concat' x q) ~ id
  issec-inv-concat' x refl refl = refl

  abstract
    is-equiv-concat' :
      (x : A) {y z : A} (q : Id y z) → is-equiv (concat' x q)
    is-equiv-concat' x q =
      is-equiv-has-inverse
        ( inv-concat' x q)
        ( issec-inv-concat' x q)
        ( isretr-inv-concat' x q)
  
  equiv-concat' :
    (x : A) {y z : A} (q : Id y z) → Id x y ≃ Id x z
  pr1 (equiv-concat' x q) = concat' x q
  pr2 (equiv-concat' x q) = is-equiv-concat' x q

convert-eq-values-htpy :
  {l1 l2 : Level} {A : UU l1} {B : UU l2} {f g : A → B} (H : f ~ g)
  (x y : A) → Id (f x) (f y) ≃ Id (g x) (g y)
convert-eq-values-htpy {f = f} {g} H x y =
  ( equiv-concat' (g x) (H y)) ∘e (equiv-concat (inv (H x)) (f y))

module _
  {l1 l2 : Level} {A : UU l1} (B : A → UU l2) {x y : A}
  where

  inv-tr : Id x y → B y → B x
  inv-tr p = tr B (inv p)

  isretr-inv-tr : (p : Id x y) → ((inv-tr p ) ∘ (tr B p)) ~ id
  isretr-inv-tr refl b = refl

  issec-inv-tr : (p : Id x y) → ((tr B p) ∘ (inv-tr p)) ~ id
  issec-inv-tr refl b = refl

  abstract
    is-equiv-tr : (p : Id x y) → is-equiv (tr B p)
    is-equiv-tr p =
      is-equiv-has-inverse
        ( inv-tr p)
        ( issec-inv-tr p)
        ( isretr-inv-tr p)

  equiv-tr : Id x y → (B x) ≃ (B y)
  pr1 (equiv-tr p) = tr B p
  pr2 (equiv-tr p) = is-equiv-tr p

module _
  {l : Level} {A : UU l} {x y z : A}
  where

  abstract
    is-equiv-inv-con :
      (p : Id x y) (q : Id y z) (r : Id x z) → is-equiv (inv-con p q r)
    is-equiv-inv-con refl q r = is-equiv-id

  equiv-inv-con :
    (p : Id x y) (q : Id y z) (r : Id x z) → Id (p ∙ q) r ≃ Id q ((inv p) ∙ r)
  pr1 (equiv-inv-con p q r) = inv-con p q r
  pr2 (equiv-inv-con p q r) = is-equiv-inv-con p q r

  abstract
    is-equiv-con-inv :
      (p : Id x y) (q : Id y z) (r : Id x z) → is-equiv (con-inv p q r)
    is-equiv-con-inv p refl r =
      is-equiv-comp'
        ( concat' p (inv right-unit))
        ( concat (inv right-unit) r)
        ( is-equiv-concat (inv right-unit) r)
        ( is-equiv-concat' p (inv right-unit))

  equiv-con-inv :
    (p : Id x y) (q : Id y z) (r : Id x z) → Id (p ∙ q) r ≃ Id p (r ∙ (inv q))
  pr1 (equiv-con-inv p q r) = con-inv p q r
  pr2 (equiv-con-inv p q r) = is-equiv-con-inv p q r
```
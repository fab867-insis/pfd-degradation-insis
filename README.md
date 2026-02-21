# PFD Dégradation — Simulateur d'architecture SIS

Outil interactif de visualisation de la **Probability of Failure on Demand (PFD)** lors d'une dégradation d'architecture de fonction instrumentée de sécurité (SIF), selon les normes **IEC 61508 / IEC 61511**.

> **Démo live** → https://insis.fr/outils-ressources/sil-pfd-test-periodique/


---

## Présentation

Généralement lorsqu'un équipement d'une SIF tombe en panne ou part en maintenance, l'exploitant souhaitera maintenir en marche son procédé. Pour cela, si l'architecture est redondante, il souhaitera dégrader l'architecture : un 2oo3 devient 2oo2 ou 1oo2, un 1oo2 devient 1oo1, etc. La PFD de la fonction évolue alors différemment, avec deux effets simultanés souvent sous-estimés :

- un **saut discontinu** à l'instant de la dégradation (les éléments restants ont accumulé une défiabilité (unreliability) depuis la mise en service — ils ne sont pas remis à neuf)
- un **changement de pente** de la courbe PFD(t), lié à la perte de redondance

L'outil permet d'explorer ces effets en faisant varier le moment de la dégradation t₀, le SIL cible, l'architecture et le taux de défaillance λdu.

---

## Paramètres simulés

| Paramètre | Valeur(s) |
|---|---|
| Période de test Ti | 8 760 h (1 an) |
| Facteur de cause commune β | 10 % |
| SIL cible | SIL 1 / SIL 2 / SIL 3 |
| PFD cible | PFDmax / 2 (une seule partie de la SIF) |
| λdu | Auto · 1E−6 /h · 1E−7 /h |

---

## Architectures disponibles

| Transition | Effet à t₀ | Note |
|---|---|---|
| 2oo3 → 2oo2 | Saut ↑ | Perte d'un équipement sur trois |
| 1oo2 → 1oo1 | Saut ↑ fort | Perte d'un équipement sur deux |
| 2oo3 → 1oo2 | Saut ↓ | **Gain de sécurité** — cas contre-intuitif |
| 2oo3 → 1oo1 | Saut ↑↑ | Cas le plus pénalisant |

Le cas **2oo3 → 1oo2** est particulièrement pédagogique : la perte d'un équipement améliore la PFD, car 1oo2 est intrinsèquement plus sûr que 2oo3 pour les défaillances dangereuses non détectées (1 combinaison de défaillance vs 3).

---

## Intégration iFrame

```html
<iframe
  src="https://VOTRE-DOMAINE.com/wp-content/uploads/pfd_degradation.html"
  width="100%"
  height="800px"
  style="border:none; border-radius:12px; display:block;"
  scrolling="no"
  loading="lazy">
</iframe>
```

L'outil communique sa hauteur réelle via `postMessage` pour permettre un redimensionnement automatique de l'iFrame. Coller ce snippet dans la page hôte (widget HTML Elementor ou équivalent) :

```html
<script>
window.addEventListener('message', function(e) {
  if (e.data && e.data.type === 'pfd-resize') {
    var iframe = document.querySelector('iframe[src*="pfd_degradation"]');
    if (iframe) iframe.style.height = (e.data.height + 20) + 'px';
  }
});
</script>
```

---

## Changelog

### v3.0
*Correctifs physiques + nouveaux modes de dégradation + paramétrage λdu*

**Correctifs physiques**

- **Temps absolu en phase dégradée** — la version initiale utilisait τ = t − t₀, revenant à supposer que les éléments restants étaient remis à neuf au moment de la panne. Les formules appliquent désormais le temps absolu t.
- **Saut discontinu visible à t₀** — conséquence du correctif précédent : la courbe dégradée ne repart plus du même point que la courbe initiale, mais d'une valeur plus haute (ou plus basse selon l'architecture), matérialisant la pénalité ou le gain immédiat lié au changement de configuration.
- **Formule 2oo2 corrigée** — la version initiale utilisait PFD = q(τ), soit un 1oo1. La formule correcte est PFD₂ₒₒ₂(t) = 2·q(t) − q²(t) = 1 − (1 − q)².

**Nouveaux modes de dégradation**

- **2oo3 → 1oo2** — saut vers le bas, courbe en vert. La dégradation améliore la sécurité (3q² → q²). Illustre le bien-fondé de certaines procédures de maintenance prévoyant ce basculement délibéré.
- **2oo3 → 1oo1** — saut maximal, perte de conformité quasi immédiate. Cas typique d'une panne concurrente à une intervention de maintenance sur la même SIF.

**Paramétrage λdu**

- Plage réaliste définie entre 1E−7 /h et 4E−6 /h.
- Trois modes : Auto (calculé pour PFDavg = PFDcible, clampé dans la plage) · 1E−6 /h · 1E−7 /h.
- En mode Auto, un avertissement s'affiche si la valeur théorique sort des bornes.

**Améliorations visuelles**

- Couleur de la courbe dégradée contextuelle : orange (saut vers le haut) ou vert (saut vers le bas).
- Légende et panneau de formules mis à jour dynamiquement selon l'architecture active.

### v1.0
*Version initiale — simulation 2oo3 → 2oo2 et 1oo2 → 1oo1, λdu calculé automatiquement.*

---

## Références

- IEC 61508 — *Functional safety of E/E/PE safety-related systems*
- IEC 61511 — *Functional safety — Safety instrumented systems for the process industry sector*
- SINTEF, PDS Method Handbook — *Reliability Data for Safety Instrumented Systems*

---

## Licence

Utilisation libre à des fins personnelles et professionnelles, sous réserve de conserver
la mention suivante visible sur toute page ou support intégrant cet outil :

> *Outil développé par Fabien CIUTAT — https://insis.fr/

Merci de ne pas supprimer ce crédit ni de redistribuer l'outil sans attribution.

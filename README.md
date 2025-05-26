# 💶 Simulateur simplifié d'impôt sur le revenu (France)

Ce projet est une **application web légère** permettant de simuler de manière simplifiée l'impôt sur le revenu annuel en France à partir de votre revenu net et des impôts déjà payés à la source.

## 🚀 Fonctionnalités

- Saisie du **revenu net annuel**
- Saisie des **impôts déjà prélevés à la source**
- Calcul de :
  - l'abattement forfaitaire de 10%
  - l'impôt dû par tranches (barème 2025)
  - le total d'impôt
  - le reste à payer
  - le taux d'imposition moyen
- Visualisation graphique de la répartition par tranche

## ⚠️ Disclaimer

> Ce projet est un **simulateur TRES simplifié**, destiné uniquement à donner une **idée générale** du fonctionnement des tranches d’imposition en France.  
> Il ne prend en compte **aucune situation fiscale particulière** : il s'applique uniquement à une **situation nominale avec une seule part fiscale** et un revenu **suffisamment éloigné du seuil de la tranche 1**.  
> Les **mécanismes de réduction d’impôt** (comme la décote, plafonnement, quotient familial ou crédits) **ne sont pas implémentés**.

## 🤖 Aide à la conception

Le développement web a été assisté [**par DevStral (Mistral AI)**](https://ollama.com/library/devstral), en particulier pour accélérer l'intégration de composants UI/UX modernes, mais **pas pour la logique fiscale**.

## 🧱 Stack technique

- [Vue 3](https://vuejs.org/)
- [Nuxt 3](https://nuxt.com/)
- [Tailwind CSS](https://tailwindcss.com/)
- [Headless UI](https://headlessui.dev/)

## 📦 Lancer en local

```bash
git clone https://github.com/guillaumevidot/calcul-impot-france.git
cd calcul-impot-france
npm install
npm run dev

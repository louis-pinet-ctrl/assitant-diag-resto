<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<meta http-equiv="Content-Security-Policy" content="default-src 'self'; script-src 'unsafe-inline' https://cdn.jsdelivr.net; style-src 'unsafe-inline'; connect-src https://api.emailjs.com">
<title>L'Assistant de Visite Restaurant</title>
<style>
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
:root{
  --jaune:#c4e913;--noir:#1e1e1e;--blanc:#FFFFFF;--gris-fond:#F6F6F6;
  --gris-texte:#6B6B6B;--vert-ok:#3CB371;--rouge-risque:#D64545;--orange-warning:#F4A435;
  --radius-card:16px;--radius-btn:12px;
}
html{font-size:16px;-webkit-text-size-adjust:100%}
body{font-family:system-ui,-apple-system,BlinkMacSystemFont,"Segoe UI",sans-serif;background:var(--gris-fond);color:var(--noir);line-height:1.6;min-height:100vh}
.app-wrapper{max-width:650px;margin:0 auto;padding:0 16px 40px;min-height:100vh;position:relative}
.progress-bar{position:fixed;top:0;left:0;right:0;height:4px;background:rgba(0,0,0,0.08);z-index:1000}
.progress-fill{height:100%;background:var(--jaune);transition:width 0.5s cubic-bezier(0.4,0,0.2,1);width:0%;border-radius:0 2px 2px 0}
.app-header{background:var(--noir);border-radius:0 0 var(--radius-card) var(--radius-card);padding:24px 24px 20px;margin:0 -16px 24px;text-align:center}
.app-header h1{color:var(--blanc);font-size:1.25rem;font-weight:600;letter-spacing:-0.01em}
.app-header .tagline{color:var(--gris-texte);font-size:0.8rem;margin-top:6px;font-weight:400}
.screen{display:none;opacity:0;transform:translateY(12px);transition:opacity 0.4s ease,transform 0.4s ease}
.screen.active{display:block}.screen.visible{opacity:1;transform:translateY(0)}
.card{background:var(--blanc);border-radius:var(--radius-card);padding:28px 24px;margin-bottom:16px;box-shadow:0 1px 3px rgba(0,0,0,0.04)}
.card h2{font-size:1.1rem;font-weight:600;margin-bottom:16px;letter-spacing:-0.01em;color:var(--noir)}
.card h3{font-size:0.95rem;font-weight:600;margin-bottom:12px;color:var(--noir)}
.card p{font-size:0.88rem;color:var(--gris-texte);margin-bottom:12px;line-height:1.65}
.card p:last-child{margin-bottom:0}
.doc-list{list-style:none;margin:16px 0}
.doc-list li{padding:10px 0;border-bottom:1px solid rgba(0,0,0,0.05);font-size:0.85rem;display:flex;align-items:flex-start;gap:10px;color:var(--noir)}
.doc-list li:last-child{border-bottom:none}
.doc-list li::before{content:"";width:6px;height:6px;min-width:6px;border-radius:50%;background:var(--jaune);margin-top:8px}
.btn{display:inline-flex;align-items:center;justify-content:center;padding:14px 24px;border-radius:var(--radius-btn);font-size:0.88rem;font-weight:500;border:none;cursor:pointer;transition:all 0.2s ease;width:100%;text-align:center}
.btn:focus-visible{outline:3px solid var(--jaune);outline-offset:2px}
.btn-primary{background:var(--jaune);color:var(--noir)}
.btn-primary:hover{filter:brightness(0.95);transform:translateY(-1px)}
.btn-secondary{background:var(--blanc);color:var(--noir);border:1.5px solid rgba(0,0,0,0.1)}
.btn-secondary:hover{border-color:var(--jaune);background:rgba(196,233,19,0.05)}
.btn-dark{background:var(--noir);color:var(--blanc)}
.btn-dark:hover{opacity:0.9;transform:translateY(-1px)}
.btn-group{display:flex;flex-direction:column;gap:10px;margin-top:20px}
.choice-card{background:var(--blanc);border:1.5px solid rgba(0,0,0,0.08);border-radius:14px;padding:18px 20px;cursor:pointer;transition:all 0.2s ease;margin-bottom:10px}
.choice-card:hover{border-color:var(--jaune);transform:translateY(-2px);box-shadow:0 4px 12px rgba(0,0,0,0.06)}
.choice-card:focus-visible{outline:3px solid var(--jaune);outline-offset:2px}
.choice-card.selected{border-color:var(--jaune);background:rgba(196,233,19,0.06)}
.choice-card .choice-title{font-size:0.9rem;font-weight:600;color:var(--noir);margin-bottom:4px}
.choice-card .choice-desc{font-size:0.8rem;color:var(--gris-texte)}
.section-header{display:flex;align-items:center;gap:10px;margin-bottom:6px}
.section-badge{font-size:0.7rem;font-weight:600;text-transform:uppercase;letter-spacing:0.05em;color:var(--gris-texte);background:var(--gris-fond);padding:4px 10px;border-radius:20px}
.question-label{font-size:0.75rem;color:var(--gris-texte);text-transform:uppercase;letter-spacing:0.05em;font-weight:500;margin-bottom:6px}
.question-text{font-size:1.05rem;font-weight:600;color:var(--noir);margin-bottom:20px;line-height:1.5}
.question-hint{font-size:0.78rem;color:var(--gris-texte);margin-top:-12px;margin-bottom:16px;font-style:italic}
.info-box{background:rgba(196,233,19,0.08);border-left:3px solid var(--jaune);border-radius:0 10px 10px 0;padding:14px 16px;margin:12px 0 16px;font-size:0.8rem;color:var(--noir);line-height:1.6}
.info-box strong{font-weight:600}
.info-box .info-title{font-weight:600;font-size:0.82rem;margin-bottom:6px;display:block}
.info-scale{margin:8px 0 4px;padding:0;list-style:none}
.info-scale li{padding:3px 0;font-size:0.78rem;display:flex;align-items:center;gap:8px}
.info-scale li .dot{width:8px;height:8px;min-width:8px;border-radius:50%;display:inline-block}
.nav-row{display:flex;gap:10px;margin-top:20px}
.nav-row .btn{flex:1}
.disclaimer{background:rgba(196,233,19,0.08);border-radius:var(--radius-btn);padding:16px;margin:16px 0;font-size:0.78rem;color:var(--gris-texte);line-height:1.6}
.disclaimer strong{color:var(--noir)}
.toast{position:fixed;bottom:30px;left:50%;transform:translateX(-50%) translateY(80px);background:var(--noir);color:var(--blanc);padding:12px 24px;border-radius:var(--radius-btn);font-size:0.82rem;opacity:0;transition:all 0.4s ease;z-index:2000;white-space:nowrap}
.toast.show{transform:translateX(-50%) translateY(0);opacity:1}
.score-section{margin-bottom:20px}
.score-section .score-label{display:flex;justify-content:space-between;align-items:center;margin-bottom:6px}
.score-section .score-name{font-size:0.85rem;font-weight:500;color:var(--noir)}
.score-section .score-value{font-size:0.8rem;font-weight:600}
.score-bar{height:10px;background:rgba(0,0,0,0.06);border-radius:8px;overflow:hidden}
.score-bar-fill{height:100%;border-radius:8px;transition:width 1s cubic-bezier(0.4,0,0.2,1);width:0%}
.global-score{text-align:center;padding:28px 20px}
.global-score .score-number{font-size:3rem;font-weight:700;letter-spacing:-0.02em}
.global-score .score-label-text{font-size:0.85rem;color:var(--gris-texte);margin-top:4px}
.alert-item{display:flex;align-items:flex-start;gap:10px;padding:12px 0;border-bottom:1px solid rgba(0,0,0,0.05);font-size:0.84rem}
.alert-item:last-child{border-bottom:none}
.alert-dot{width:8px;height:8px;min-width:8px;border-radius:50%;margin-top:6px}
.alert-dot.red{background:var(--rouge-risque)}.alert-dot.orange{background:var(--orange-warning)}.alert-dot.green{background:var(--vert-ok)}
.result-checklist{list-style:none;margin:12px 0}
.result-checklist li{padding:8px 0;font-size:0.84rem;display:flex;align-items:flex-start;gap:8px}
.result-checklist li .check-icon{font-size:0.9rem;margin-top:1px}
.inconnu-item{display:flex;align-items:flex-start;gap:10px;padding:10px 0;border-bottom:1px solid rgba(0,0,0,0.05);font-size:0.84rem}
.inconnu-item:last-child{border-bottom:none}
.profile-summary{background:var(--gris-fond);border-radius:10px;padding:14px 16px;margin:12px 0;font-size:0.82rem;line-height:1.7}
.profile-tag{display:inline-block;background:var(--jaune);color:var(--noir);font-size:0.72rem;font-weight:600;padding:3px 10px;border-radius:20px;margin:3px 4px 3px 0;letter-spacing:0.02em}
.profile-tag.warning{background:var(--orange-warning);color:var(--blanc)}
.profile-tag.danger{background:var(--rouge-risque);color:var(--blanc)}
.text-input{width:100%;padding:12px 16px;border:1.5px solid rgba(0,0,0,0.1);border-radius:var(--radius-btn);font-size:0.88rem;font-family:inherit;transition:border-color 0.2s;margin-top:8px}
.text-input:focus{outline:none;border-color:var(--jaune)}
.text-input::placeholder{color:var(--gris-texte);opacity:0.7}
.step-indicator{display:flex;align-items:center;gap:8px;margin-bottom:20px}
.step-dot{width:28px;height:28px;border-radius:50%;display:flex;align-items:center;justify-content:center;font-size:0.72rem;font-weight:700;border:2px solid rgba(0,0,0,0.1);color:var(--gris-texte);background:var(--blanc);transition:all 0.3s}
.step-dot.active{border-color:var(--jaune);background:var(--jaune);color:var(--noir)}
.step-dot.done{border-color:var(--vert-ok);background:var(--vert-ok);color:var(--blanc)}
.step-line{flex:1;height:2px;background:rgba(0,0,0,0.08)}
.step-line.done{background:var(--vert-ok)}
.simulator-cta{background:linear-gradient(135deg,var(--jaune) 0%,#d4f034 100%);border-radius:var(--radius-card);padding:22px 24px;margin:16px 0;cursor:pointer;transition:all 0.2s;text-decoration:none;display:block;border:none;width:100%;text-align:left}
.simulator-cta:hover{transform:translateY(-2px);box-shadow:0 6px 20px rgba(196,233,19,0.35)}
.simulator-cta .cta-title{font-size:0.95rem;font-weight:700;color:var(--noir);margin-bottom:4px;display:flex;align-items:center;gap:8px}
.simulator-cta .cta-desc{font-size:0.82rem;color:rgba(30,30,30,0.75);line-height:1.5}
.simulator-cta .cta-arrow{margin-left:auto;font-size:1.1rem}
.form-grid{display:grid;grid-template-columns:1fr 1fr;gap:12px;margin:16px 0}
.form-group{display:flex;flex-direction:column}
.form-group.full-width{grid-column:1/-1}
.form-label{font-size:0.78rem;font-weight:600;color:var(--noir);margin-bottom:4px;letter-spacing:0.02em}
.form-label .required{color:var(--rouge-risque);margin-left:2px}
.form-input{width:100%;padding:11px 14px;border:1.5px solid rgba(0,0,0,0.1);border-radius:var(--radius-btn);font-size:0.86rem;font-family:inherit;transition:border-color 0.2s}
.form-input:focus{outline:none;border-color:var(--jaune)}
.form-input::placeholder{color:var(--gris-texte);opacity:0.6}
.form-input.error{border-color:var(--rouge-risque)}
.form-select{width:100%;padding:11px 14px;border:1.5px solid rgba(0,0,0,0.1);border-radius:var(--radius-btn);font-size:0.86rem;font-family:inherit;transition:border-color 0.2s;background:#fff;appearance:none;-webkit-appearance:none;background-image:url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='12' height='8'%3E%3Cpath d='M1 1l5 5 5-5' stroke='%23666' stroke-width='1.5' fill='none'/%3E%3C/svg%3E");background-repeat:no-repeat;background-position:right 14px center;cursor:pointer}
.form-select:focus{outline:none;border-color:var(--jaune)}
.form-select.error{border-color:var(--rouge-risque)}
.form-separator{grid-column:1/-1;border:none;border-top:1px solid rgba(0,0,0,0.08);margin:8px 0}
.form-section-title{grid-column:1/-1;font-size:0.82rem;font-weight:700;color:var(--noir);margin:4px 0 -4px 0;letter-spacing:0.02em}
.form-textarea{resize:vertical;min-height:70px}
.form-error{font-size:0.72rem;color:var(--rouge-risque);margin-top:3px;min-height:16px}
.form-note{font-size:0.76rem;color:var(--gris-texte);line-height:1.5;margin-top:12px;text-align:center}
.form-sending{opacity:0.6;pointer-events:none}
.contact-cta-card{background:var(--noir);border-radius:var(--radius-card);padding:28px 24px;margin-bottom:16px;text-align:center}
.contact-cta-card h3{color:var(--blanc);font-size:1.05rem;font-weight:600;margin-bottom:8px}
.contact-cta-card p{color:rgba(255,255,255,0.7);font-size:0.84rem;margin-bottom:20px;line-height:1.6}
.contact-btns{display:flex;gap:10px}
.contact-btns a{flex:1;display:inline-flex;align-items:center;justify-content:center;gap:8px;padding:14px 20px;border-radius:var(--radius-btn);font-size:0.86rem;font-weight:600;text-decoration:none;transition:all 0.2s;cursor:pointer}
.btn-whatsapp{background:#25D366;color:#fff}
.btn-whatsapp:hover{background:#1ebe57;transform:translateY(-1px)}
.btn-contact{background:var(--jaune);color:var(--noir)}
.btn-contact:hover{filter:brightness(0.95);transform:translateY(-1px)}
@media(max-width:480px){.contact-btns{flex-direction:column}}
@media(max-width:480px){.form-grid{grid-template-columns:1fr}}
@media(max-width:480px){.app-wrapper{padding:0 12px 32px}.app-header{margin:0 -12px 20px;padding:20px 16px 16px}.card{padding:22px 18px}}
@media print{.progress-bar,.btn,.nav-row,.btn-group{display:none!important}.screen:not(.screen-result){display:none!important}.screen.screen-result{display:block!important;opacity:1!important;transform:none!important}}
</style>
</head>
<body>
<div class="progress-bar"><div class="progress-fill" id="progressFill"></div></div>
<div class="app-wrapper">
  <header class="app-header">
    <h1>L'Assistant de Visite Restaurant</h1>
    <div class="tagline">L'outil qui transforme une visite en audit terrain structur&eacute;</div>
  </header>
  <main id="appMain" role="main"></main>
</div>
<div class="toast" id="toast" role="status" aria-live="polite"></div>

<script>
(function(){
"use strict";

const MAX_SCORE = 3;
const TOAST_DURATION = 2500;
const TRANSITION_DELAY = 300;
const ANIM_DELAY = 30;
const BAR_ANIM_DELAY = 200;

/* ─── STATE ─── */
const state = {
  currentScreen: 0,
  /* Step 1: Operational profile */
  restaurationType: null,      /* traditionnelle, rapide, street_food, gastronomique, bistronomique, franchise */
  franchiseur: "",
  cookingType: null,            /* classique, cuisson_douce, sans_cuisson, mixte */
  needsExtraction: null,        /* oui, non, ne_sais_pas */
  grandeCuisine: null,          /* oui, non, ne_sais_pas */
  /* Step 2: Legal framework */
  projectType: null,            /* cession_fonds, droit_bail */
  /* Audit */
  answers: {},
  totalQuestions: 0
};

/* ─── DERIVED STATE HELPERS ─── */
function isFranchise(){ return state.restaurationType === "franchise"; }
function isCession(){ return state.projectType === "cession_fonds"; }
function isDroitBail(){ return state.projectType === "droit_bail"; }
function needsExtraction(){ return state.needsExtraction !== "non"; }
function isGrandeCuisine(){ return state.grandeCuisine === "oui"; }
function hasFriture(){
  const a = state.answers["ext_friture"];
  return a && a.value === "friture_oui";
}
function isCuissonIntensive(){
  return state.cookingType === "classique" || state.cookingType === "mixte";
}

/* ─── SECTIONS & QUESTIONS ─── */
const sections = [
  {
    id: "bail", name: "Bail commercial", icon: "\u{1F4C4}",
    questions: [
      { id: "bail_duree", text: "Quelle est la dur\u00e9e restante du bail ?", hint: "Un bail court (<3 ans) limite la visibilit\u00e9 et peut impacter le financement bancaire.", type: "choice",
        options: [
          { label: "Plus de 6 ans", value: "ok", score: 3 },
          { label: "Entre 3 et 6 ans", value: "moyen", score: 2 },
          { label: "Moins de 3 ans", value: "risque", score: 1 },
          { label: "Je ne sais pas encore", value: "inconnu", score: 0 }
        ], alert_if: ["risque"], alert_msg: "Bail court : risque de non-renouvellement ou d\u2019augmentation de loyer. V\u00e9rifiez les conditions de renouvellement." },
      { id: "bail_destination", text: "La clause de destination du bail autorise-t-elle votre activit\u00e9 de restauration ?", hint: "V\u00e9rifiez que le bail permet l\u2019activit\u00e9 envisag\u00e9e (restauration, bar, traiteur\u2026).", type: "choice",
        infoBox: { title: "La destination du bail : un point essentiel", text: "C\u2019est l\u2019un des premiers \u00e9l\u00e9ments que Ma\u00eetre Pinet v\u00e9rifie. La clause de destination d\u00e9termine pr\u00e9cis\u00e9ment l\u2019activit\u00e9 que vous pouvez exercer dans le local. Prenons un exemple : un bail autorisant la \u00ab\u00a0restauration traditionnelle\u00a0\u00bb ne vous permet pas d\u2019exploiter une restauration rapide. Cette distinction a des cons\u00e9quences directes sur votre activit\u00e9, vos horaires, votre client\u00e8le et vos obligations r\u00e9glementaires. Un bail trop restrictif peut bloquer votre projet." },
        options: [
          { label: "Oui, activit\u00e9 restauration explicitement autoris\u00e9e", value: "ok", score: 3 },
          { label: "Clause \u00abtous commerces\u00bb ou assimil\u00e9e", value: "moyen", score: 2 },
          { label: "Non ou je ne suis pas s\u00fbr", value: "risque", score: 0 },
          { label: "Je n\u2019ai pas encore le bail", value: "inconnu", score: 0 }
        ], alert_if: ["risque"], alert_msg: "Clause de destination restrictive : risque de r\u00e9siliation si activit\u00e9 non conforme." },
      { id: "bail_loyer", text: "Le loyer vous semble-t-il coh\u00e9rent avec le march\u00e9 local ?", hint: "Comparez avec les loyers pratiqu\u00e9s dans le quartier pour la m\u00eame surface.", type: "choice",
        infoBox: { title: "Le taux d\u2019effort, c\u2019est quoi ?", text: "Le taux d\u2019effort correspond au ratio loyer annuel / chiffre d\u2019affaires HT. Il permet de mesurer le poids du loyer sur votre activit\u00e9.",
          scale: [
            { label: "7 \u00e0 8\u00a0% \u2014 Loyer raisonnable", color: "var(--vert-ok)" },
            { label: "9\u00a0% \u2014 Loyer \u00e9lev\u00e9, vigilance", color: "var(--orange-warning)" },
            { label: "10\u00a0% \u2014 Segment premium, justifiable si fort flux", color: "var(--orange-warning)" },
            { label: "> 10\u00a0% hors centre commercial \u2014 Risque r\u00e9el", color: "var(--rouge-risque)" }
          ] },
        options: [
          { label: "Oui, dans la fourchette du march\u00e9 (< 8\u00a0%)", value: "ok", score: 3 },
          { label: "L\u00e9g\u00e8rement au-dessus (8-10\u00a0%)", value: "moyen", score: 2 },
          { label: "Nettement au-dessus (> 10\u00a0%) ou je ne sais pas", value: "risque", score: 1 },
          { label: "Je n\u2019ai pas encore cette information", value: "inconnu", score: 0 }
        ], alert_if: ["risque"], alert_msg: "Taux d\u2019effort sup\u00e9rieur \u00e0 10\u00a0% hors centre commercial : risque important pour la viabilit\u00e9 financi\u00e8re." },
      { id: "bail_charges", text: "Avez-vous le d\u00e9tail des charges locatives (provisions sur charges, taxe fonci\u00e8re, TEOM) ?", type: "choice",
        options: [
          { label: "Oui, d\u00e9tail clair et raisonnable", value: "ok", score: 3 },
          { label: "Partiellement, certaines zones d\u2019ombre", value: "moyen", score: 2 },
          { label: "Non, pas encore transmis", value: "risque", score: 1 }
        ] },
      { id: "bail_travaux", text: "Le bail pr\u00e9voit-il une clause sur les travaux que peut r\u00e9aliser le locataire ?", hint: "Certains baux interdisent les travaux sans accord du bailleur.", type: "choice",
        options: [
          { label: "Travaux libres avec information du bailleur", value: "ok", score: 3 },
          { label: "Travaux soumis \u00e0 accord pr\u00e9alable du bailleur", value: "moyen", score: 2 },
          { label: "Travaux tr\u00e8s limit\u00e9s ou interdits", value: "risque", score: 1 },
          { label: "Je ne sais pas encore", value: "inconnu", score: 0 }
        ], alert_if: ["risque"], alert_msg: "Restrictions travaux : anticipez les difficult\u00e9s pour am\u00e9nager le local." },
      { id: "bail_franchise_clause", text: "Le bail contient-il une clause d\u2019agr\u00e9ment du franchiseur ou des restrictions li\u00e9es \u00e0 l\u2019enseigne ?",
        hint: "Certains bailleurs imposent des restrictions sur l\u2019enseigne ou exigent l\u2019agr\u00e9ment du r\u00e9seau.",
        type: "choice",
        options: [
          { label: "Pas de restriction, enseigne libre", value: "ok", score: 3 },
          { label: "Clause d\u2019agr\u00e9ment mais g\u00e9rable", value: "moyen", score: 2 },
          { label: "Restrictions fortes ou incompatibles", value: "risque", score: 1 },
          { label: "Je ne sais pas", value: "inconnu", score: 0 }
        ],
        alert_if: ["risque"], alert_msg: "Le bail impose des restrictions d\u2019enseigne incompatibles avec votre franchise. V\u00e9rifiez avec le franchiseur et le bailleur.",
        condition: function(){ return isFranchise(); }
      }
    ]
  },
  {
    id: "copro", name: "Copropri\u00e9t\u00e9", icon: "\u{1F3E2}",
    questions: [
      { id: "copro_travaux_communs", text: "Des travaux de copropri\u00e9t\u00e9 sont-ils pr\u00e9vus (ravalement, toiture, parties communes) ?", hint: "Demandez les PV d\u2019AG des 3 derni\u00e8res ann\u00e9es.", type: "choice",
        options: [
          { label: "Non, rien de pr\u00e9vu", value: "ok", score: 3 },
          { label: "Travaux mineurs pr\u00e9vus", value: "moyen", score: 2 },
          { label: "Travaux importants vot\u00e9s ou envisag\u00e9s", value: "risque", score: 1 },
          { label: "Je n\u2019ai pas les PV d\u2019AG", value: "inconnu", score: 0 }
        ], alert_if: ["risque"], alert_msg: "Travaux de copropri\u00e9t\u00e9 importants pr\u00e9vus : appels de fonds \u00e0 anticiper." }
    ]
  },
  {
    id: "securite", name: "S\u00e9curit\u00e9 & ERP", icon: "\u{1F512}",
    questions: [
      { id: "erp_categorie", text: "Connaissez-vous la cat\u00e9gorie ERP du local ?", hint: "La cat\u00e9gorie d\u00e9pend de la capacit\u00e9 d\u2019accueil et d\u00e9termine les obligations de s\u00e9curit\u00e9.", type: "choice",
        options: [
          { label: "Oui, cat\u00e9gorie 5 (petit ERP)", value: "ok", score: 3 },
          { label: "Oui, cat\u00e9gorie 1 \u00e0 4 (obligations renforc\u00e9es)", value: "moyen", score: 2 },
          { label: "Non, je ne connais pas la cat\u00e9gorie", value: "inconnu", score: 0 }
        ] },
      { id: "erp_commission", text: "Quel est le dernier avis de la commission de s\u00e9curit\u00e9 ?", type: "choice",
        options: [
          { label: "Avis favorable r\u00e9cent (< 3 ans)", value: "ok", score: 3 },
          { label: "Avis favorable ancien (> 3 ans)", value: "moyen", score: 2 },
          { label: "Avis d\u00e9favorable ou pas d\u2019information", value: "risque", score: 0 },
          { label: "Non applicable (cat. 5)", value: "ok", score: 3 }
        ], alert_if: ["risque"], alert_msg: "Commission de s\u00e9curit\u00e9 d\u00e9favorable : mise en conformit\u00e9 obligatoire avant ouverture." },
      { id: "erp_secours", text: "Les issues de secours sont-elles conformes et d\u00e9gag\u00e9es ?", type: "choice",
        options: [
          { label: "Oui, conformes et accessibles", value: "ok", score: 3 },
          { label: "Partiellement, am\u00e9nagements n\u00e9cessaires", value: "moyen", score: 2 },
          { label: "Non ou je n\u2019ai pas v\u00e9rifi\u00e9", value: "risque", score: 1 }
        ] },
      { id: "erp_accessibilite", text: "Le local est-il accessible aux personnes \u00e0 mobilit\u00e9 r\u00e9duite (PMR) ?", hint: "Obligation l\u00e9gale pour les ERP \u2014 des d\u00e9rogations existent sous conditions.", type: "choice",
        options: [
          { label: "Oui, conforme ou Ad\u2019AP en cours", value: "ok", score: 3 },
          { label: "Partiellement, travaux n\u00e9cessaires", value: "moyen", score: 2 },
          { label: "Non conforme, pas de d\u00e9rogation", value: "risque", score: 1 },
          { label: "Je ne sais pas", value: "inconnu", score: 0 }
        ] },
      { id: "erp_grande_cuisine", text: "La puissance totale des appareils de cuisson d\u00e9passe-t-elle 20\u00a0kW ?",
        hint: "Au-del\u00e0 de 20\u00a0kW, le local est class\u00e9 \u00abgrande cuisine\u00bb (GC) avec des obligations suppl\u00e9mentaires : dispositif d\u2019arr\u00eat d\u2019urgence, extinction automatique, ventilation renforc\u00e9e.",
        type: "choice",
        infoBox: { title: "Seuil \u00abgrande cuisine\u00bb (GC)", text: "L\u2019arr\u00eat\u00e9 du 25 juin 1980 impose des mesures sp\u00e9cifiques pour les locaux dont la puissance totale des appareils de cuisson ou de remise en temp\u00e9rature d\u00e9passe 20\u00a0kW. Cela inclut notamment un syst\u00e8me d\u2019extinction automatique, un dispositif d\u2019arr\u00eat d\u2019urgence de l\u2019alimentation en \u00e9nergie, et un conduit d\u2019extraction conforme." },
        options: [
          { label: "Oui, puissance > 20\u00a0kW (grande cuisine)", value: "risque", score: 1 },
          { label: "Non, puissance \u2264 20\u00a0kW", value: "ok", score: 3 },
          { label: "Je ne sais pas encore", value: "inconnu", score: 0 }
        ],
        alert_if: ["risque"], alert_msg: "Grande cuisine (> 20\u00a0kW) : obligations renforc\u00e9es en mati\u00e8re de s\u00e9curit\u00e9 incendie, ventilation et extinction automatique.",
        condition: function(){ return isGrandeCuisine() || state.grandeCuisine === "ne_sais_pas"; }
      }
    ]
  },
  {
    id: "extraction", name: "Extraction & Ventilation", icon: "\u{1F4A8}",
    condition: function(){ return needsExtraction(); },
    questions: [
      { id: "ext_type", text: "Quel est le type de conduite d\u2019extraction ?", hint: "L\u2019extraction doit id\u00e9alement \u00eatre en conduite ext\u00e9rieure jusqu\u2019en toiture.", type: "choice",
        infoBox: { title: "Pourquoi c\u2019est important ?", text: "L\u2019extraction est l\u2019un des points les plus sensibles en restauration. Le conduit doit id\u00e9alement d\u00e9boucher en toiture (obligation RSD) et se situer \u00e0 plus de 8\u00a0m\u00e8tres de toute fen\u00eatre. Un conduit trop proche des ouvrants ou ne d\u00e9bouchant pas en toiture expose \u00e0 des plaintes de voisinage et \u00e0 une non-conformit\u00e9 administrative pouvant emp\u00eacher l\u2019exploitation." },
        options: [
          { label: "Conduite ext\u00e9rieure en toiture", value: "ok", score: 3 },
          { label: "Conduite int\u00e9rieure (gaine dans l\u2019immeuble)", value: "moyen", score: 2 },
          { label: "Pas de conduite d\u2019extraction visible", value: "risque", score: 0 },
          { label: "Je ne sais pas", value: "inconnu", score: 0 }
        ], alert_if: ["risque"], alert_msg: "Absence d\u2019extraction : point bloquant majeur." },
      { id: "ext_chemisage", text: "La conduite d\u2019extraction est-elle chemis\u00e9e (tubage inox int\u00e9rieur) ?", hint: "Le chemisage en inox prot\u00e8ge le conduit et facilite l\u2019entretien.", type: "choice",
        options: [
          { label: "Oui, conduite chemis\u00e9e inox", value: "ok", score: 3 },
          { label: "Non chemis\u00e9e mais en bon \u00e9tat", value: "moyen", score: 2 },
          { label: "Non chemis\u00e9e et \u00e9tat incertain", value: "risque", score: 1 },
          { label: "Je ne sais pas", value: "inconnu", score: 0 }
        ] },
      { id: "ext_diametre", text: "Quel est le diam\u00e8tre du conduit d\u2019extraction ?", hint: "Le diam\u00e8tre conditionne directement le type de cuisine exploitable.", type: "choice",
        infoBox: { title: "Rep\u00e8res diam\u00e8tre / activit\u00e9",
          scale: [
            { label: "200\u00a0mm \u2014 Cuisine l\u00e9g\u00e8re (sandwicherie, saladerie)", color: "var(--vert-ok)" },
            { label: "300\u00a0mm \u2014 Cuisine classique, cuisson mod\u00e9r\u00e9e", color: "var(--vert-ok)" },
            { label: "400\u00a0mm \u2014 Friture ou cuisine intensive", color: "var(--orange-warning)" },
            { label: "< 200\u00a0mm \u2014 Insuffisant pour la plupart des activit\u00e9s", color: "var(--rouge-risque)" }
          ] },
        options: [
          { label: "400\u00a0mm ou plus", value: "ok", score: 3 },
          { label: "300\u00a0mm", value: "moyen", score: 2 },
          { label: "200\u00a0mm", value: "moyen", score: 2 },
          { label: "Moins de 200\u00a0mm ou je ne sais pas", value: "risque", score: 1 }
        ] },
      { id: "ext_friture", text: "Allez-vous faire de la friture dans votre activit\u00e9 ?", hint: "La friture impose un diam\u00e8tre minimum de 300-400\u00a0mm et un syst\u00e8me de filtration renforc\u00e9.", type: "choice",
        options: [
          { label: "Oui, friture pr\u00e9vue", value: "friture_oui", score: 2 },
          { label: "Non, pas de friture", value: "ok", score: 3 },
          { label: "Je ne sais pas encore", value: "inconnu", score: 1 }
        ], alert_if: ["friture_oui"], alert_msg: "Friture pr\u00e9vue : v\u00e9rifiez diam\u00e8tre \u2265 300\u00a0mm et filtration adapt\u00e9e.",
        condition: function(){ return isCuissonIntensive(); }
      },
      { id: "ext_rsd", text: "Disposez-vous du certificat de conformit\u00e9 RSD et du carnet d\u2019entretien ?", type: "choice",
        options: [
          { label: "Oui, les deux documents \u00e0 jour", value: "ok", score: 3 },
          { label: "Carnet d\u2019entretien uniquement", value: "moyen", score: 2 },
          { label: "Aucun document disponible", value: "risque", score: 0 },
          { label: "Je ne sais pas", value: "inconnu", score: 0 }
        ], alert_if: ["risque"], alert_msg: "Absence de certificat RSD : extraction non certifi\u00e9e conforme." },
    ]
  },
  {
    id: "sanitaire", name: "Sanitaire & Hygi\u00e8ne", icon: "\u{1F9FC}",
    questions: [
      { id: "san_marche_avant", text: "La cuisine respecte-t-elle le principe de la marche en avant ?", hint: "Les flux (propre/sale, chaud/froid) ne doivent pas se croiser.", type: "choice",
        options: [
          { label: "Oui, flux bien s\u00e9par\u00e9s", value: "ok", score: 3 },
          { label: "Partiellement, am\u00e9nagements possibles", value: "moyen", score: 2 },
          { label: "Non, refonte n\u00e9cessaire", value: "risque", score: 1 },
          { label: "Je n\u2019ai pas pu v\u00e9rifier", value: "inconnu", score: 0 }
        ] },
      { id: "san_bac_graisse", text: "Un bac \u00e0 graisse est-il install\u00e9 et entretenu ?", type: "choice",
        options: [
          { label: "Oui, avec contrat d\u2019entretien", value: "ok", score: 3 },
          { label: "Oui, mais entretien \u00e0 v\u00e9rifier", value: "moyen", score: 2 },
          { label: "Non install\u00e9", value: "risque", score: 1 },
          { label: "Je ne sais pas", value: "inconnu", score: 0 }
        ] },
      { id: "san_vestiaires", text: "Le local dispose-t-il de vestiaires et sanitaires pour le personnel ?", type: "choice",
        options: [
          { label: "Oui, conformes", value: "ok", score: 3 },
          { label: "Oui, mais \u00e0 remettre aux normes", value: "moyen", score: 2 },
          { label: "Non", value: "risque", score: 1 }
        ] }
    ]
  },
  {
    id: "travaux", name: "Travaux & \u00c9tat du local", icon: "\u{1F527}",
    questions: [
      { id: "trav_etat", text: "Quel est l\u2019\u00e9tat g\u00e9n\u00e9ral du local (sols, murs, plafonds, plomberie, \u00e9lectricit\u00e9) ?", type: "choice",
        options: [
          { label: "Bon \u00e9tat, peu de travaux", value: "ok", score: 3 },
          { label: "Correct, rafra\u00eechissement n\u00e9cessaire", value: "moyen", score: 2 },
          { label: "Travaux importants n\u00e9cessaires", value: "risque", score: 1 }
        ] },
      { id: "trav_estimation", text: "Avez-vous une estimation du co\u00fbt des travaux n\u00e9cessaires ?", hint: "Consultez un architecte ou ma\u00eetre d\u2019\u0153uvre sp\u00e9cialis\u00e9 en restauration.", type: "choice",
        options: [
          { label: "Oui, devis ou estimation r\u00e9aliste", value: "ok", score: 3 },
          { label: "Estimation approximative", value: "moyen", score: 2 },
          { label: "Pas encore d\u2019estimation", value: "risque", score: 1 }
        ] },
      { id: "trav_autorisation", text: "Les travaux n\u00e9cessitent-ils des autorisations sp\u00e9cifiques ?", hint: "Permis de construire, d\u00e9claration pr\u00e9alable, accord copropri\u00e9t\u00e9, accord bailleur\u2026", type: "choice",
        options: [
          { label: "Travaux l\u00e9gers, pas d\u2019autorisation", value: "ok", score: 3 },
          { label: "Autorisations n\u00e9cessaires mais anticip\u00e9es", value: "moyen", score: 2 },
          { label: "Autorisations complexes ou incertaines", value: "risque", score: 1 },
          { label: "Je ne sais pas encore", value: "inconnu", score: 0 }
        ] },
      { id: "trav_delais", text: "Les d\u00e9lais de travaux sont-ils compatibles avec votre calendrier d\u2019ouverture ?", type: "choice",
        options: [
          { label: "Oui, planning r\u00e9aliste", value: "ok", score: 3 },
          { label: "Serr\u00e9 mais faisable", value: "moyen", score: 2 },
          { label: "Incompatible ou incertain", value: "risque", score: 1 },
          { label: "Pas encore \u00e9valu\u00e9", value: "inconnu", score: 0 }
        ] },
      { id: "trav_franchise_cahier", text: "Les travaux sont-ils conformes au cahier des charges du franchiseur ?",
        hint: "Le franchiseur impose g\u00e9n\u00e9ralement des normes d\u2019am\u00e9nagement pr\u00e9cises (enseigne, mobilier, mat\u00e9riaux, agencement).",
        type: "choice",
        options: [
          { label: "Oui, cahier des charges respect\u00e9", value: "ok", score: 3 },
          { label: "Partiellement, adaptations n\u00e9cessaires", value: "moyen", score: 2 },
          { label: "Non conforme ou pas de cahier des charges re\u00e7u", value: "risque", score: 1 }
        ],
        alert_if: ["risque"], alert_msg: "Travaux non conformes au cahier des charges du franchiseur : risque de refus d\u2019agr\u00e9ment du local.",
        condition: function(){ return isFranchise(); }
      }
    ]
  },
  {
    id: "materiel", name: "Mat\u00e9riel & Stock", icon: "\u{1F373}",
    questions: [
      { id: "mat_concept", text: "Reprenez-vous le concept existant du c\u00e9dant ou changez-vous compl\u00e8tement de concept ?",
        hint: "En cession de fonds, vous rachetez un ensemble d\u2019actifs (mat\u00e9riel, client\u00e8le, bail\u2026). Le concept peut \u00eatre modifi\u00e9 librement apr\u00e8s la reprise.",
        type: "choice",
        options: [
          { label: "Je reprends le m\u00eame concept ou tr\u00e8s proche", value: "ok", score: 3 },
          { label: "Je modifie partiellement le concept", value: "moyen", score: 2 },
          { label: "Je change compl\u00e8tement de concept", value: "risque", score: 1 }
        ], alert_if: ["risque"], alert_msg: "Changement de concept : r\u00e9\u00e9valuez mat\u00e9riel et client\u00e8le dans le prix de cession.",
        condition: function(){ return isCession(); } },
      { id: "mat_type", text: "Quel type de mat\u00e9riel est compris dans la cession ?", type: "choice",
        options: [
          { label: "Gros \u00e9quipement complet", value: "ok", score: 3 },
          { label: "Gros \u00e9quipement partiel + petit mat\u00e9riel", value: "moyen", score: 2 },
          { label: "Essentiellement du petit mat\u00e9riel", value: "risque", score: 1 },
          { label: "Je n\u2019ai pas encore l\u2019inventaire", value: "inconnu", score: 0 }
        ], condition: function(){ return isCession(); } },
      { id: "mat_etat", text: "Quel est l\u2019\u00e9tat g\u00e9n\u00e9ral du mat\u00e9riel ?", type: "choice",
        options: [
          { label: "Mat\u00e9riel r\u00e9cent et en bon \u00e9tat", value: "ok", score: 3 },
          { label: "Mat\u00e9riel fonctionnel mais vieillissant", value: "moyen", score: 2 },
          { label: "Mat\u00e9riel v\u00e9tuste, remplacement \u00e0 pr\u00e9voir", value: "risque", score: 1 },
          { label: "Je ne sais pas", value: "inconnu", score: 0 }
        ], condition: function(){ return isCession(); } },
      { id: "mat_credit_bail", text: "Certains \u00e9quipements sont-ils en cr\u00e9dit-bail (leasing) ?",
        hint: "Un mat\u00e9riel en cr\u00e9dit-bail n\u2019appartient pas au c\u00e9dant.",
        type: "choice",
        options: [
          { label: "Non, tout en propri\u00e9t\u00e9", value: "ok", score: 3 },
          { label: "Oui, quelques \u00e9quipements identifi\u00e9s", value: "moyen", score: 2 },
          { label: "Oui, \u00e9quipements importants en cr\u00e9dit-bail", value: "risque", score: 1 },
          { label: "Je ne sais pas", value: "inconnu", score: 0 }
        ], alert_if: ["risque"], alert_msg: "Cr\u00e9dit-bail sur \u00e9quipements importants : hors actif c\u00e9d\u00e9.",
        condition: function(){ return isCession(); } },
      { id: "mat_inventaire", text: "La liste du mat\u00e9riel est-elle d\u00e9taill\u00e9e et valoris\u00e9e ?", type: "choice",
        options: [
          { label: "Oui, inventaire complet avec \u00e9tat et valeur", value: "ok", score: 3 },
          { label: "Liste partielle, sans valorisation", value: "moyen", score: 2 },
          { label: "Pas de liste", value: "risque", score: 1 }
        ], condition: function(){ return isCession(); } },
      { id: "mat_stock", text: "Le stock de marchandises est-il compris dans le prix de cession ?",
        hint: "En cession de fonds, le stock est g\u00e9n\u00e9ralement pay\u00e9 en sus.", type: "choice",
        options: [
          { label: "Oui, stock inclus dans le prix", value: "ok", score: 3 },
          { label: "Stock pay\u00e9 en sus, montant estim\u00e9", value: "moyen", score: 2 },
          { label: "Stock pay\u00e9 en sus, montant non d\u00e9fini", value: "risque", score: 1 },
          { label: "Je ne sais pas", value: "inconnu", score: 0 }
        ], condition: function(){ return isCession(); } },
      { id: "mat_franchise_equipement", text: "Le mat\u00e9riel existant est-il compatible avec les exigences du franchiseur ?",
        hint: "Le franchiseur peut imposer des marques, mod\u00e8les ou fournisseurs sp\u00e9cifiques.",
        type: "choice",
        options: [
          { label: "Oui, mat\u00e9riel conforme aux exigences", value: "ok", score: 3 },
          { label: "Partiellement, quelques remplacements \u00e0 pr\u00e9voir", value: "moyen", score: 2 },
          { label: "Non, remplacement important n\u00e9cessaire", value: "risque", score: 1 },
          { label: "Je ne sais pas", value: "inconnu", score: 0 }
        ],
        alert_if: ["risque"], alert_msg: "Mat\u00e9riel non conforme aux exigences du franchiseur : budget de remplacement \u00e0 pr\u00e9voir.",
        condition: function(){ return isFranchise() && isCession(); }
      }
    ]
  },
  {
    id: "operationnel", name: "Op\u00e9rationnel & Juridique", icon: "\u2696\uFE0F",
    questions: [
      { id: "op_licence", text: "Le local dispose-t-il d\u2019une licence de d\u00e9bit de boissons ?", type: "choice",
        options: [
          { label: "Oui, licence IV", value: "ok", score: 3 },
          { label: "Oui, licence III ou licence restaurant", value: "moyen", score: 2 },
          { label: "Non, pas de licence", value: "risque", score: 1 },
          { label: "Je ne sais pas", value: "inconnu", score: 0 }
        ], condition: function(){ return isCession(); } },
      { id: "op_terrasse", text: "Le restaurant dispose-t-il d\u2019une terrasse avec convention d\u2019occupation ?", type: "choice",
        options: [
          { label: "Oui, convention en cours", value: "ok", score: 3 },
          { label: "Oui, mais convention \u00e0 renouveler", value: "moyen", score: 2 },
          { label: "Pas de terrasse", value: "ok", score: 2 },
          { label: "Terrasse sans convention", value: "risque", score: 0 }
        ], condition: function(){ return isCession(); } },
      { id: "op_salaries", text: "Y a-t-il des salari\u00e9s \u00e0 reprendre (art. L.1224-1 C. trav.) ?", type: "choice",
        options: [
          { label: "Oui, liste et contrats transmis", value: "ok", score: 3 },
          { label: "Oui, mais informations partielles", value: "moyen", score: 2 },
          { label: "Pas de salari\u00e9", value: "ok", score: 3 },
          { label: "Je ne sais pas", value: "inconnu", score: 0 }
        ], condition: function(){ return isCession(); } },
      { id: "op_contrats", text: "Avez-vous la liste des contrats en cours ?", type: "choice",
        options: [
          { label: "Oui, liste compl\u00e8te transmise", value: "ok", score: 3 },
          { label: "Partiellement", value: "moyen", score: 2 },
          { label: "Non", value: "risque", score: 1 }
        ], condition: function(){ return isCession(); } },
      { id: "op_nantissement", text: "Existe-t-il un nantissement sur le fonds de commerce ?", type: "choice",
        infoBox: { title: "Qu\u2019est-ce qu\u2019un nantissement ?", text: "Le nantissement est une s\u00fbret\u00e9 prise par un cr\u00e9ancier (g\u00e9n\u00e9ralement une banque) sur le fonds de commerce. Il fonctionne comme une hypoth\u00e8que : si le d\u00e9biteur ne rembourse pas, le cr\u00e9ancier peut faire saisir et vendre le fonds. En cas de cession, les cr\u00e9anciers inscrits doivent \u00eatre d\u00e9sint\u00e9ress\u00e9s ou donner mainlev\u00e9e. L\u2019\u00e9tat des nantissements est consultable sur Infogreffe pour environ 50\u00a0\u20ac. C\u2019est au c\u00e9dant de fournir ce document et de justifier de la situation du fonds." },
        options: [
          { label: "Non, fonds libre", value: "ok", score: 3 },
          { label: "Oui, mais identifi\u00e9 et g\u00e9rable", value: "moyen", score: 2 },
          { label: "Oui, situation complexe", value: "risque", score: 0 },
          { label: "Je ne sais pas", value: "inconnu", score: 0 }
        ], alert_if: ["risque"], alert_msg: "Nantissement : peut bloquer la cession.",
        condition: function(){ return isCession(); } },
      { id: "op_bilans", text: "Avez-vous analys\u00e9 les trois derniers bilans comptables ?", type: "choice",
        options: [
          { label: "Oui, bilans coh\u00e9rents", value: "ok", score: 3 },
          { label: "Oui, mais points \u00e0 \u00e9claircir", value: "moyen", score: 2 },
          { label: "Non, pas encore re\u00e7us", value: "risque", score: 1 }
        ], condition: function(){ return isCession(); } },
      { id: "op_franchise_contrat", text: "Avez-vous v\u00e9rifi\u00e9 la compatibilit\u00e9 entre le contrat de franchise et le bail commercial ?",
        hint: "Le contrat de franchise peut imposer une dur\u00e9e minimale de bail, une clause de non-concurrence, ou des conditions de sortie sp\u00e9cifiques.",
        type: "choice",
        options: [
          { label: "Oui, les deux contrats sont compatibles", value: "ok", score: 3 },
          { label: "En cours de v\u00e9rification", value: "moyen", score: 2 },
          { label: "Non v\u00e9rifi\u00e9 ou incompatibilit\u00e9s d\u00e9tect\u00e9es", value: "risque", score: 1 },
          { label: "Je ne sais pas", value: "inconnu", score: 0 }
        ],
        alert_if: ["risque"], alert_msg: "Incompatibilit\u00e9 bail / contrat de franchise : risque juridique majeur. Faites v\u00e9rifier par un avocat.",
        condition: function(){ return isFranchise(); }
      },
      { id: "op_franchise_dip", text: "Avez-vous re\u00e7u le DIP (Document d\u2019Information Pr\u00e9contractuel) du franchiseur ?",
        hint: "Le DIP est obligatoire (art. L.330-3 C. com.) et doit \u00eatre remis au moins 20 jours avant la signature du contrat de franchise.",
        type: "choice",
        options: [
          { label: "Oui, DIP re\u00e7u et analys\u00e9", value: "ok", score: 3 },
          { label: "Oui, mais pas encore analys\u00e9 en d\u00e9tail", value: "moyen", score: 2 },
          { label: "Non, pas encore re\u00e7u", value: "risque", score: 0 }
        ],
        alert_if: ["risque"], alert_msg: "DIP non re\u00e7u : obligation l\u00e9gale du franchiseur. Ne signez rien avant r\u00e9ception et analyse compl\u00e8te.",
        condition: function(){ return isFranchise(); }
      }
    ]
  }
];

/* ─── DOM HELPERS ─── */
function createEl(tag, cls){ const e = document.createElement(tag); if(cls) e.className = cls; return e; }
function createText(tag, cls, txt){ const e = createEl(tag, cls); e.textContent = txt; return e; }
function showToast(msg){ const t = document.getElementById("toast"); t.textContent = msg; t.classList.add("show"); setTimeout(function(){ t.classList.remove("show"); }, TOAST_DURATION); }
function updateProgress(pct){ document.getElementById("progressFill").style.width = pct + "%"; }
function getScoreColor(pct){ return pct >= 70 ? "var(--vert-ok)" : pct >= 40 ? "var(--orange-warning)" : "var(--rouge-risque)"; }

function copyText(text){
  if(navigator.clipboard && typeof navigator.clipboard.writeText === "function"){
    navigator.clipboard.writeText(text).then(function(){ showToast("Email copi\u00e9 dans le presse-papiers"); }, function(){ fallbackCopy(text); });
  } else { fallbackCopy(text); }
}
function fallbackCopy(text){
  const ta = document.createElement("textarea"); ta.value = text; ta.setAttribute("aria-hidden","true");
  ta.style.cssText = "position:fixed;left:-9999px;top:-9999px;opacity:0";
  document.body.appendChild(ta); ta.focus(); ta.select();
  try{ document.execCommand("copy") ? showToast("Email copi\u00e9 dans le presse-papiers") : showToast("Copie impossible"); } catch(e){ showToast("Copie impossible"); }
  document.body.removeChild(ta);
}

function buildSimulatorCTA(){
  const a = createEl("a","simulator-cta");
  a.href = "https://www.louispinetavocat.fr/contenus/simulateur-valorisation-restaurant";
  a.target = "_blank"; a.rel = "noopener";
  const title = createEl("div","cta-title");
  title.appendChild(document.createTextNode("\u{1F4CA} Simulateur de valorisation restaurant"));
  const arrow = createText("span","cta-arrow","\u2192"); title.appendChild(arrow);
  a.appendChild(title);
  a.appendChild(createText("div","cta-desc","Estimez le vrai prix du fonds de commerce avant de vous engager. Gratuit et imm\u00e9diat."));
  return a;
}

function createChoiceCard(label, desc, selected, onClick){
  const ch = createEl("div", "choice-card" + (selected ? " selected" : ""));
  ch.setAttribute("role","radio"); ch.setAttribute("aria-checked", selected ? "true" : "false"); ch.setAttribute("tabindex","0");
  ch.appendChild(createText("div","choice-title", label));
  if(desc) ch.appendChild(createText("div","choice-desc", desc));
  ch.addEventListener("click", onClick);
  ch.addEventListener("keydown", function(e){ if(e.key === "Enter" || e.key === " "){ e.preventDefault(); onClick(); } });
  return ch;
}

function buildInfoBox(ib){
  const box = createEl("div","info-box");
  if(ib.title) box.appendChild(createText("span","info-title", ib.title));
  if(ib.text) box.appendChild(createText("span","", ib.text));
  if(ib.scale){ const ul = createEl("ul","info-scale"); ib.scale.forEach(function(s){ const li = createEl("li"); const dot = createEl("span","dot"); dot.style.background = s.color; li.appendChild(dot); li.appendChild(document.createTextNode(s.label)); ul.appendChild(li); }); box.appendChild(ul); }
  return box;
}

function selectInGroup(group, selected){
  group.querySelectorAll(".choice-card").forEach(function(el){ el.classList.remove("selected"); el.setAttribute("aria-checked","false"); });
  selected.classList.add("selected"); selected.setAttribute("aria-checked","true");
}

function getActiveQuestions(){
  const qs = [];
  sections.forEach(function(sec){
    if(sec.condition && !sec.condition()) return;
    sec.questions.forEach(function(q){
      if(!q.condition || q.condition()) qs.push({ section: sec, question: q });
    });
  });
  return qs;
}

function getEmailText(pt){
  const h = "Objet : Demande de documents pr\u00e9alable \u00e0 la visite du local\n\nMadame, Monsieur,\n\n";
  const f = "\n\nJe vous remercie par avance pour la communication de ces \u00e9l\u00e9ments.\n\nBien cordialement,";
  if(pt === "cession_fonds") return h + "Dans le cadre de mon projet de reprise de fonds de commerce :\n\n- Bail commercial (avec avenants)\n- R\u00e8glement de copropri\u00e9t\u00e9\n- PV d\u2019AG (3 derni\u00e8res ann\u00e9es)\n- Trois derniers bilans comptables\n- Contrats en cours\n- Autorisations administratives\n- Liste des \u00e9quipements et mat\u00e9riels\n- Engagements li\u00e9s au fonds (nantissement, cr\u00e9dits-bails)\n- Contrats de travail repris\n- Certificat RSD + carnet d\u2019entretien\n- Taxe fonci\u00e8re et charges" + f;
  return h + "Dans le cadre de mon projet d\u2019installation :\n\n- Bail commercial existant (si cession de droit au bail)\n- R\u00e8glement de copropri\u00e9t\u00e9\n- PV d\u2019AG (3 derni\u00e8res ann\u00e9es)\n- Taxe fonci\u00e8re et charges" + f;
}

/* ─── PROFILE SUMMARY ─── */
function getProfileSummary(){
  const tags = [];
  const typeLabels = { rapide:"Restauration rapide", street_food:"Street food", gastronomique:"Gastronomique", bistronomique:"Bistronomique", traditionnelle:"Traditionnelle / Classique", franchise:"Franchise" };
  const cookLabels = { classique:"Cuisson classique", cuisson_douce:"Cuisson douce", sans_cuisson:"Sans cuisson", mixte:"Cuisson mixte" };
  if(state.restaurationType) tags.push({ label: typeLabels[state.restaurationType] || state.restaurationType });
  if(state.franchiseur) tags.push({ label: "Franchiseur : " + state.franchiseur });
  if(state.cookingType) tags.push({ label: cookLabels[state.cookingType] || state.cookingType });
  if(state.needsExtraction === "oui") tags.push({ label: "Extraction n\u00e9cessaire" });
  if(state.needsExtraction === "non") tags.push({ label: "Pas d\u2019extraction" });
  if(state.grandeCuisine === "oui") tags.push({ label: "Grande cuisine (> 20\u00a0kW)", cls: "warning" });
  const legalLabels = { cession_fonds:"Cession de fonds de commerce", droit_bail:"Droit au bail / Nouveau bail" };
  if(state.projectType) tags.push({ label: legalLabels[state.projectType] });
  return tags;
}

function renderProfileTags(container){
  const tags = getProfileSummary();
  if(tags.length === 0) return;
  const div = createEl("div","profile-summary");
  tags.forEach(function(t){
    const tag = createText("span","profile-tag" + (t.cls ? " " + t.cls : ""), t.label);
    div.appendChild(tag);
  });
  container.appendChild(div);
}

/* ─── SCREEN MANAGEMENT ─── */
let screens = [];
let currentIdx = 0;

function renderApp(){
  screens = []; currentIdx = 0;
  state.answers = {}; state.totalQuestions = 0;
  state.restaurationType = null; state.franchiseur = "";
  state.cookingType = null; state.needsExtraction = null;
  state.grandeCuisine = null; state.projectType = null;
  screens.push({ type: "intro" });
  screens.push({ type: "op_step_1" });
  screens.push({ type: "op_step_2" });
  screens.push({ type: "op_step_3" });
  screens.push({ type: "op_step_4" });
  screens.push({ type: "legal_type" });
  screens.push({ type: "documents" });
  showScreen(0);
}

function buildQuestionScreens(){
  screens = screens.slice(0, 7);
  const qs = getActiveQuestions();
  state.totalQuestions = qs.length;
  qs.forEach(function(item, i){ screens.push({ type: "question", data: item, index: i }); });
  screens.push({ type: "lead_form" });
  screens.push({ type: "result" });
}

function showScreen(idx){
  currentIdx = idx;
  const main = document.getElementById("appMain"); main.innerHTML = "";
  const screen = screens[idx];
  const div = createEl("div","screen active" + (screen.type === "result" ? " screen-result" : ""));
  switch(screen.type){
    case "intro": renderIntro(div); break;
    case "op_step_1": renderOpStep1(div); break;
    case "op_step_2": renderOpStep2(div); break;
    case "op_step_3": renderOpStep3(div); break;
    case "op_step_4": renderOpStep4(div); break;
    case "legal_type": renderLegalType(div); break;
    case "documents": renderDocuments(div); break;
    case "question": renderQuestion(div, screen); break;
    case "lead_form": renderLeadForm(div); break;
    case "result": renderResult(div); break;
  }
  main.appendChild(div);
  setTimeout(function(){ div.classList.add("visible"); }, ANIM_DELAY);
  const total = screens.length;
  updateProgress(total > 1 ? Math.round((idx / (total - 1)) * 100) : 0);
  window.scrollTo({ top: 0, behavior: "smooth" });
}

/* ─── STEP INDICATOR ─── */
function renderStepIndicator(container, activeStep){
  const steps = ["Projet","Cadre juridique","Documents","Audit"];
  const bar = createEl("div","step-indicator");
  steps.forEach(function(s, i){
    if(i > 0){ const line = createEl("div","step-line" + (i < activeStep ? " done" : "")); bar.appendChild(line); }
    const dot = createText("div","step-dot" + (i < activeStep ? " done" : i === activeStep ? " active" : ""), String(i + 1));
    dot.setAttribute("aria-label", s);
    bar.appendChild(dot);
  });
  container.appendChild(bar);
}

/* ─── PAGE 1: INTRO ─── */
function renderIntro(container){
  const hero = createEl("div","card");
  const h2 = createText("h2","","Ce local est-il le bon pour votre restaurant ?");
  h2.style.cssText = "font-size:1.2rem;line-height:1.4;margin-bottom:14px"; hero.appendChild(h2);
  const p1 = createText("p","","Un d\u00e9tail oubli\u00e9 lors de la visite, c\u2019est un probl\u00e8me \u00e0 50\u202f000\u00a0\u20ac dans six mois.");
  p1.style.cssText = "font-size:0.92rem;color:var(--noir);line-height:1.7;margin-bottom:14px"; hero.appendChild(p1);
  const p2 = createText("p","","Extraction non conforme, bail qui ne couvre pas votre activit\u00e9, nantissement cach\u00e9, charges sous-estim\u00e9es, mat\u00e9riel en cr\u00e9dit-bail\u2026 Les pi\u00e8ges sont techniques, juridiques, et souvent invisibles \u00e0 l\u2019\u0153il nu.");
  p2.style.cssText = "font-size:0.88rem;color:var(--gris-texte);line-height:1.7"; hero.appendChild(p2);
  container.appendChild(hero);

  const whatCard = createEl("div","card");
  whatCard.appendChild(createText("h3","","En 5 minutes, vous saurez"));
  const ul = createEl("ul","doc-list");
  ["Un score de visite, section par section","Les alertes concr\u00e8tes sur les risques identifi\u00e9s","Les points \u00e0 creuser avant de vous engager","Les prochaines \u00e9tapes adapt\u00e9es \u00e0 votre profil"].forEach(function(t){ ul.appendChild(createText("li","",t)); });
  whatCard.appendChild(ul);
  const adapt = createText("p","","Les questions s\u2019adaptent automatiquement \u00e0 votre type de projet : restauration traditionnelle, rapide, franchise, cession de fonds ou droit au bail.");
  adapt.style.cssText = "font-size:0.84rem;color:var(--gris-texte);line-height:1.6;margin-top:12px"; whatCard.appendChild(adapt);
  container.appendChild(whatCard);

  /* Social proof */
  const proofCard = createEl("div","card");
  proofCard.style.cssText = "background:var(--noir);color:var(--blanc);text-align:center;padding:24px";
  const proofTitle = createText("p","","Con\u00e7u par Ma\u00eetre Louis Pinet");
  proofTitle.style.cssText = "font-weight:700;font-size:0.95rem;margin-bottom:10px;color:var(--jaune)"; proofCard.appendChild(proofTitle);
  const proofDesc = createText("p","","Avocat d\u00e9di\u00e9 aux restaurateurs \u2014 cessions de fonds de commerce, baux commerciaux et extraction.");
  proofDesc.style.cssText = "font-size:0.82rem;color:rgba(255,255,255,0.75);margin-bottom:16px;line-height:1.6"; proofCard.appendChild(proofDesc);
  const stats = createEl("div"); stats.style.cssText = "display:flex;justify-content:center;gap:28px";
  [["Bail","N\u00e9gociation"],["Fonds de commerce","Cession"],["Extraction","Diagnostic"]].forEach(function(s){
    const st = createEl("div"); st.style.cssText = "text-align:center";
    const n = createText("div","",s[0]); n.style.cssText = "font-size:0.95rem;font-weight:700;color:var(--jaune)"; st.appendChild(n);
    const l = createText("div","",s[1]); l.style.cssText = "font-size:0.7rem;color:rgba(255,255,255,0.6);margin-top:2px"; st.appendChild(l);
    stats.appendChild(st);
  });
  proofCard.appendChild(stats);
  container.appendChild(proofCard);

  const disc = createEl("div","disclaimer");
  disc.appendChild(document.createTextNode("Cet outil ne remplace pas l\u2019intervention d\u2019un architecte, d\u2019un ma\u00eetre d\u2019\u0153uvre ou d\u2019un expert-comptable.")); container.appendChild(disc);

  const bg = createEl("div","btn-group");
  const btn = createText("button","btn btn-primary","V\u00e9rifier si ce local est le bon \u2192");
  btn.addEventListener("click", function(){ showScreen(1); }); bg.appendChild(btn); container.appendChild(bg);
}

/* ─── STEP 1a: RESTAURANT TYPE ─── */
function renderOpStep1(container){
  renderStepIndicator(container, 0);
  const card = createEl("div","card");
  card.appendChild(createText("div","question-label","\u00c9tape 1 \u2014 Votre projet op\u00e9rationnel"));
  card.appendChild(createText("div","question-text","Quel type de restauration envisagez-vous ?"));

  const group = createEl("div"); group.setAttribute("role","radiogroup"); group.setAttribute("aria-label","Type de restauration");
  const types = [
    { value: "traditionnelle", title: "Restauration traditionnelle / Classique", desc: "Cuisine faite maison, brasserie, caf\u00e9-restaurant, service \u00e0 table, carte vari\u00e9e." },
    { value: "bistronomique", title: "Bistronomique", desc: "Cuisine gastronomique dans un cadre d\u00e9contract\u00e9, produits de qualit\u00e9." },
    { value: "gastronomique", title: "Gastronomique", desc: "Haute cuisine, service haut de gamme, produits d\u2019exception." },
    { value: "rapide", title: "Restauration rapide", desc: "Service au comptoir, consommation sur place ou \u00e0 emporter." },
    { value: "street_food", title: "Street food / Snacking", desc: "Offre nomade, sandwicherie, saladerie, poke, bagels\u2026" },
    { value: "franchise", title: "Franchise / R\u00e9seau", desc: "Ouverture sous enseigne d\u2019un r\u00e9seau de franchise." }
  ];
  types.forEach(function(t){
    const ch = createChoiceCard(t.title, t.desc, state.restaurationType === t.value, function(){
      state.restaurationType = t.value;
      selectInGroup(group, ch);
      var ff = document.getElementById("franchiseField");
      if(ff) ff.style.display = t.value === "franchise" ? "block" : "none";
    });
    group.appendChild(ch);
  });
  card.appendChild(group);

  var franchiseField = createEl("div"); franchiseField.id = "franchiseField";
  franchiseField.style.display = state.restaurationType === "franchise" ? "block" : "none";
  franchiseField.style.marginTop = "12px";
  franchiseField.appendChild(createText("div","question-hint","Indiquez le nom du franchiseur :"));
  var input = createEl("input","text-input"); input.type = "text"; input.placeholder = "Ex. : McDonald\u2019s, Burger King, Pokawa\u2026";
  input.value = state.franchiseur;
  input.addEventListener("input", function(){ state.franchiseur = input.value; });
  franchiseField.appendChild(input);
  card.appendChild(franchiseField);
  container.appendChild(card);

  var bg = createEl("div","btn-group");
  var btnNext = createText("button","btn btn-primary","Continuer \u2192");
  btnNext.addEventListener("click", function(){
    if(!state.restaurationType){ showToast("Choisissez votre type de restauration"); return; }
    showScreen(currentIdx + 1);
  });
  bg.appendChild(btnNext);
  var btnBack = createText("button","btn btn-secondary","\u2190 Retour");
  btnBack.addEventListener("click", function(){ showScreen(currentIdx - 1); }); bg.appendChild(btnBack);
  container.appendChild(bg);
}

/* ─── STEP 1b: COOKING TYPE ─── */
function renderOpStep2(container){
  renderStepIndicator(container, 0);
  const card = createEl("div","card");
  card.appendChild(createText("div","question-label","\u00c9tape 1 \u2014 Votre projet op\u00e9rationnel"));
  card.appendChild(createText("div","question-text","Quel type de cuisson allez-vous pratiquer ?"));
  card.appendChild(createText("div","question-hint","Cela d\u00e9termine les besoins en extraction et les obligations de s\u00e9curit\u00e9."));

  const group = createEl("div"); group.setAttribute("role","radiogroup"); group.setAttribute("aria-label","Type de cuisson");
  [
    { value: "classique", title: "Cuisson classique", desc: "Four, plaques, friteuse, grill\u2026 Production de fum\u00e9es et graisses." },
    { value: "cuisson_douce", title: "Cuisson douce / basse temp\u00e9rature", desc: "Sous-vide, vapeur, bain-marie. Peu de production de fum\u00e9es." },
    { value: "mixte", title: "Cuisson mixte", desc: "Combinaison de cuisson classique et douce selon les plats." },
    { value: "sans_cuisson", title: "Sans cuisson / assemblage", desc: "Saladerie, poke bowls, sandwicherie froide. Pas de cuisson sur place." }
  ].forEach(function(t){
    const ch = createChoiceCard(t.title, t.desc, state.cookingType === t.value, function(){
      state.cookingType = t.value; selectInGroup(group, ch);
    });
    group.appendChild(ch);
  });
  card.appendChild(group);
  container.appendChild(card);

  var bg = createEl("div","btn-group");
  var btnNext = createText("button","btn btn-primary","Continuer \u2192");
  btnNext.addEventListener("click", function(){
    if(!state.cookingType){ showToast("Choisissez votre type de cuisson"); return; }
    showScreen(currentIdx + 1);
  });
  bg.appendChild(btnNext);
  var btnBack = createText("button","btn btn-secondary","\u2190 Retour");
  btnBack.addEventListener("click", function(){ showScreen(currentIdx - 1); }); bg.appendChild(btnBack);
  container.appendChild(bg);
}

/* ─── STEP 1c: EXTRACTION ─── */
function renderOpStep3(container){
  renderStepIndicator(container, 0);
  const card = createEl("div","card");
  card.appendChild(createText("div","question-label","\u00c9tape 1 \u2014 Votre projet op\u00e9rationnel"));
  card.appendChild(createText("div","question-text","Votre activit\u00e9 n\u00e9cessite-t-elle une extraction ?"));
  card.appendChild(buildInfoBox({ title: "Quand a-t-on besoin d\u2019extraction ?", text: "Toute activit\u00e9 de cuisson g\u00e9n\u00e9rant des fum\u00e9es, vapeurs ou graisses n\u00e9cessite un syst\u00e8me d\u2019extraction conforme au RSD. Une saladerie ou sandwicherie froide peut parfois s\u2019en passer, mais une simple ventilation ne suffit g\u00e9n\u00e9ralement pas d\u00e8s qu\u2019il y a cuisson." }));

  const group = createEl("div"); group.setAttribute("role","radiogroup"); group.setAttribute("aria-label","Extraction n\u00e9cessaire");
  [{ value: "oui", title: "Oui, extraction n\u00e9cessaire", desc: "Cuisson sur place avec production de fum\u00e9es/graisses." },
   { value: "non", title: "Non, pas d\u2019extraction", desc: "Assemblage froid uniquement, pas de cuisson." },
   { value: "ne_sais_pas", title: "Je ne sais pas encore", desc: "Un diagnostic sera n\u00e9cessaire." }
  ].forEach(function(t){
    const ch = createChoiceCard(t.title, t.desc, state.needsExtraction === t.value, function(){
      state.needsExtraction = t.value; selectInGroup(group, ch);
    });
    group.appendChild(ch);
  });
  card.appendChild(group);
  container.appendChild(card);

  var bg = createEl("div","btn-group");
  var btnNext = createText("button","btn btn-primary","Continuer \u2192");
  btnNext.addEventListener("click", function(){
    if(!state.needsExtraction){ showToast("Indiquez si vous avez besoin d\u2019extraction"); return; }
    showScreen(currentIdx + 1);
  });
  bg.appendChild(btnNext);
  var btnBack = createText("button","btn btn-secondary","\u2190 Retour");
  btnBack.addEventListener("click", function(){ showScreen(currentIdx - 1); }); bg.appendChild(btnBack);
  container.appendChild(bg);
}

/* ─── STEP 1d: GRANDE CUISINE ─── */
function renderOpStep4(container){
  renderStepIndicator(container, 0);
  const card = createEl("div","card");
  card.appendChild(createText("div","question-label","\u00c9tape 1 \u2014 Votre projet op\u00e9rationnel"));
  card.appendChild(createText("div","question-text","Allez-vous d\u00e9passer 20\u00a0kW de puissance totale d\u2019appareils de cuisson ?"));
  card.appendChild(buildInfoBox({ title: "Seuil \u00abgrande cuisine\u00bb", text: "Au-del\u00e0 de 20\u00a0kW, le local est class\u00e9 \u00abgrande cuisine\u00bb avec des obligations suppl\u00e9mentaires : extinction automatique, arr\u00eat d\u2019urgence, ventilation renforc\u00e9e (arr\u00eat\u00e9 du 25 juin 1980).",
    scale: [
      { label: "\u2264 20\u00a0kW \u2014 R\u00e9glementation ERP standard", color: "var(--vert-ok)" },
      { label: "> 20\u00a0kW \u2014 Obligations \u00abgrande cuisine\u00bb suppl\u00e9mentaires", color: "var(--rouge-risque)" }
    ] }));

  const group = createEl("div"); group.setAttribute("role","radiogroup"); group.setAttribute("aria-label","Grande cuisine");
  [{ value: "oui", title: "Oui, puissance > 20\u00a0kW", desc: "Cuisine intensive avec \u00e9quipements lourds (piano, friteuses, four)." },
   { value: "non", title: "Non, puissance \u2264 20\u00a0kW", desc: "Cuisine l\u00e9g\u00e8re ou mod\u00e9r\u00e9e." },
   { value: "ne_sais_pas", title: "Je ne sais pas encore", desc: "Il faudra faire le calcul avec votre liste d\u2019\u00e9quipements." }
  ].forEach(function(t){
    const ch = createChoiceCard(t.title, t.desc, state.grandeCuisine === t.value, function(){
      state.grandeCuisine = t.value; selectInGroup(group, ch);
    });
    group.appendChild(ch);
  });
  card.appendChild(group);
  container.appendChild(card);

  var bg = createEl("div","btn-group");
  var btnNext = createText("button","btn btn-primary","Continuer \u2192");
  btnNext.addEventListener("click", function(){
    if(!state.grandeCuisine){ showToast("Indiquez si vous d\u00e9passerez 20\u00a0kW"); return; }
    showScreen(currentIdx + 1);
  });
  bg.appendChild(btnNext);
  var btnBack = createText("button","btn btn-secondary","\u2190 Retour");
  btnBack.addEventListener("click", function(){ showScreen(currentIdx - 1); }); bg.appendChild(btnBack);
  container.appendChild(bg);
}

/* ─── STEP 2: LEGAL FRAMEWORK ─── */
function renderLegalType(container){
  renderStepIndicator(container, 1);
  renderProfileTags(container);

  const card = createEl("div","card");
  card.appendChild(createText("div","question-label","\u00c9tape 2 \u2014 Cadre juridique"));
  card.appendChild(createText("div","question-text","Quel est le montage juridique de votre projet ?"));

  const group = createEl("div"); group.setAttribute("role","radiogroup"); group.setAttribute("aria-label","Type de projet");
  const choices = [
    { value: "cession_fonds", title: "Cession de fonds de commerce", desc: "Rachat de l\u2019actif : client\u00e8le, bail, mat\u00e9riel, enseigne, licence, salari\u00e9s\u2026" },
    { value: "droit_bail", title: "Droit au bail ou local nu (nouveau bail)", desc: "Reprise du bail existant (cession de droit au bail) ou signature d\u2019un bail neuf sur un local vacant. Pas de reprise d\u2019activit\u00e9 existante." }
  ];
  choices.forEach(function(c){
    const ch = createChoiceCard(c.title, c.desc, state.projectType === c.value, function(){
      state.projectType = c.value; selectInGroup(group, ch);
    });
    group.appendChild(ch);
  });
  card.appendChild(group); container.appendChild(card);

  const disc = createEl("div","disclaimer");
  disc.appendChild(createText("strong","","Quelle diff\u00e9rence ? "));
  disc.appendChild(document.createTextNode("En cession de fonds de commerce, vous rachetez un actif comprenant la client\u00e8le, le droit au bail, le mat\u00e9riel, l\u2019enseigne, la licence et les contrats de travail (art. L.1224-1 C. trav.). En droit au bail, vous reprenez uniquement le bail commercial d\u2019un locataire sortant. En local nu, vous signez un bail directement avec le bailleur. Dans ces deux derniers cas, il n\u2019y a pas de reprise d\u2019activit\u00e9 existante."));
  container.appendChild(disc);

  /* Simulator CTA — financial info box */
  const finBox = createEl("div","info-box"); finBox.style.marginTop = "8px";
  finBox.appendChild(createText("span","info-title","\u{1F4B0} Prix et \u00e9l\u00e9ments financiers"));
  finBox.appendChild(createText("span","","Cet outil audite le local, pas les finances. Pour estimer si le prix demand\u00e9 par le c\u00e9dant est coh\u00e9rent, utilisez notre simulateur de valorisation :"));
  container.appendChild(finBox);
  container.appendChild(buildSimulatorCTA());

  const bg = createEl("div","btn-group");
  const btnNext = createText("button","btn btn-primary","Continuer \u2192");
  btnNext.addEventListener("click", function(){
    if(!state.projectType){ showToast("Choisissez le cadre juridique"); return; }
    showScreen(currentIdx + 1);
  });
  bg.appendChild(btnNext);
  const btnBack = createText("button","btn btn-secondary","\u2190 Retour");
  btnBack.addEventListener("click", function(){ showScreen(currentIdx - 1); }); bg.appendChild(btnBack);
  container.appendChild(bg);
}

/* ─── DOCUMENTS ─── */
function renderDocuments(container){
  renderStepIndicator(container, 2);
  renderProfileTags(container);

  const docCard = createEl("div","card");
  let h3Text, pText, docs;
  if(isCession()){
    h3Text = "Documents \u00e0 demander au c\u00e9dant"; pText = "Avant toute visite, demandez les documents suivants :";
    docs = ["Bail commercial (avec avenants)","R\u00e8glement de copropri\u00e9t\u00e9","PV d\u2019AG (3 derni\u00e8res ann\u00e9es)","Trois derniers bilans comptables","Contrats en cours","Autorisations administratives","Liste des \u00e9quipements et mat\u00e9riels","Engagements (nantissement, cr\u00e9dits-bails)","Contrats de travail repris","Certificat RSD + carnet d\u2019entretien","Taxe fonci\u00e8re et charges"];
    if(isFranchise()) docs.push("Contrat de franchise et DIP","Cahier des charges d\u2019am\u00e9nagement du franchiseur");
  } else {
    h3Text = "Documents \u00e0 demander"; pText = "Avant toute visite :";
    docs = ["Bail commercial existant (si cession de droit au bail)","R\u00e8glement de copropri\u00e9t\u00e9","PV d\u2019AG (3 derni\u00e8res ann\u00e9es)","Taxe fonci\u00e8re et charges"];
  }
  docCard.appendChild(createText("h3","",h3Text));
  docCard.appendChild(createText("p","",pText));
  const ul = createEl("ul","doc-list");
  docs.forEach(function(d){ ul.appendChild(createText("li","",d)); });
  docCard.appendChild(ul); container.appendChild(docCard);

  if(isCession()) container.appendChild(buildSimulatorCTA());

  const bg = createEl("div","btn-group");
  const btnCopy = createText("button","btn btn-secondary","Copier l\u2019email de demande de documents");
  btnCopy.addEventListener("click", function(){ copyText(getEmailText(state.projectType)); }); bg.appendChild(btnCopy);
  const btnNext = createText("button","btn btn-primary","Commencer l\u2019audit \u2192");
  btnNext.addEventListener("click", function(){ buildQuestionScreens(); showScreen(currentIdx + 1); }); bg.appendChild(btnNext);
  const btnBack = createText("button","btn btn-secondary","\u2190 Retour");
  btnBack.addEventListener("click", function(){ showScreen(currentIdx - 1); }); bg.appendChild(btnBack);
  container.appendChild(bg);
}

/* ─── QUESTIONS ─── */
function renderQuestion(container, screen){
  const sec = screen.data.section, q = screen.data.question;
  const qIdx = screen.index, total = state.totalQuestions;

  renderStepIndicator(container, 3);

  const card = createEl("div","card");
  const hdr = createEl("div","section-header");
  hdr.appendChild(createText("span","section-badge", sec.icon + " " + sec.name)); card.appendChild(hdr);
  card.appendChild(createText("div","question-label","Question " + (qIdx + 1) + " sur " + total));
  card.appendChild(createText("div","question-text", q.text));
  if(q.hint) card.appendChild(createText("div","question-hint", q.hint));
  if(q.infoBox) card.appendChild(buildInfoBox(q.infoBox));

  const selectedValue = state.answers[q.id] ? state.answers[q.id].value : null;
  const group = createEl("div"); group.setAttribute("role","radiogroup"); group.setAttribute("aria-label", q.text);
  q.options.forEach(function(opt){
    const ch = createChoiceCard(opt.label, null, selectedValue === opt.value, function(){
      state.answers[q.id] = { value: opt.value, score: opt.score, maxScore: MAX_SCORE, sectionId: sec.id };
      selectInGroup(group, ch);
      setTimeout(function(){ if(currentIdx < screens.length - 1) showScreen(currentIdx + 1); }, TRANSITION_DELAY);
    });
    group.appendChild(ch);
  });
  card.appendChild(group); container.appendChild(card);

  const nav = createEl("div","nav-row");
  const btnBack = createText("button","btn btn-secondary","\u2190");
  btnBack.style.flex = "0 0 60px"; btnBack.setAttribute("aria-label","Question pr\u00e9c\u00e9dente");
  btnBack.addEventListener("click", function(){ showScreen(currentIdx - 1); }); nav.appendChild(btnBack);
  if(currentIdx < screens.length - 1){
    const btnSkip = createText("button","btn btn-secondary","Passer cette question");
    btnSkip.addEventListener("click", function(){ showScreen(currentIdx + 1); }); nav.appendChild(btnSkip);
  }
  container.appendChild(nav);
}

/* ─── EMAILJS CONFIG ─── */
const EMAILJS_SERVICE_ID = "service_p4n5irf";
const EMAILJS_TEMPLATE_ID = "template_k0e282e";
const EMAILJS_PUBLIC_KEY = "3MND6tW8YvzuBeowW";
let emailjsReady = false;

function initEmailJS(){
  if(emailjsReady || typeof emailjs === "undefined") return;
  emailjs.init(EMAILJS_PUBLIC_KEY);
  emailjsReady = true;
}

function buildAuditSummaryForEmail(){
  const lines = [];
  const tags = getProfileSummary();
  lines.push("PROFIL : " + tags.map(function(t){ return t.label; }).join(" | "));
  lines.push("");
  const activeQs = getActiveQuestions();
  let totalScore = 0, totalMax = 0;
  const sectionData = {};
  activeQs.forEach(function(item){
    const q = item.question, sec = item.section, a = state.answers[q.id];
    if(!sectionData[sec.id]) sectionData[sec.id] = { name: sec.name, score: 0, max: 0, alerts: [], unknowns: [] };
    if(a){
      sectionData[sec.id].score += a.score; sectionData[sec.id].max += MAX_SCORE;
      totalScore += a.score; totalMax += MAX_SCORE;
      if(q.alert_if && q.alert_msg && q.alert_if.indexOf(a.value) !== -1) sectionData[sec.id].alerts.push(q.alert_msg);
      if(a.value === "inconnu") sectionData[sec.id].unknowns.push(q.text);
    }
  });
  const globalPct = totalMax > 0 ? Math.round((totalScore / totalMax) * 100) : 0;
  lines.push("SCORE GLOBAL : " + globalPct + "%");
  lines.push("");
  Object.keys(sectionData).forEach(function(k){
    const s = sectionData[k]; if(s.max === 0) return;
    const pct = Math.round((s.score / s.max) * 100);
    lines.push(s.name + " : " + pct + "%");
    s.alerts.forEach(function(a){ lines.push("  \u26a0 " + a); });
    s.unknowns.forEach(function(u){ lines.push("  ? " + u); });
  });
  return lines.join("\n");
}

function sendLeadData(formData, onSuccess, onError){
  initEmailJS();
  if(typeof emailjs === "undefined"){ onError("EmailJS non charg\u00e9"); return; }
  const params = {
    prospect_nom: formData.nom,
    prospect_prenom: formData.prenom,
    prospect_email: formData.email,
    prospect_telephone: formData.telephone,
    prospect_profil: formData.profil,
    prospect_nb_restos: formData.nb_restos,
    prospect_ville: formData.ville,
    prospect_surface: formData.surface || "Non renseign\u00e9",
    prospect_prix_cession: formData.prix || "Non renseign\u00e9",
    prospect_commentaire: formData.commentaire || "",
    projet_type: (function(){ var l = { cession_fonds:"Cession de fonds de commerce", droit_bail:"Droit au bail / Nouveau bail" }; return l[state.projectType] || state.projectType; })(),
    projet_restauration: state.restaurationType || "",
    projet_cuisson: state.cookingType || "",
    projet_extraction: state.needsExtraction || "",
    projet_grande_cuisine: state.grandeCuisine || "",
    projet_franchiseur: state.franchiseur || "",
    audit_resume: buildAuditSummaryForEmail()
  };
  emailjs.send(EMAILJS_SERVICE_ID, EMAILJS_TEMPLATE_ID, params)
    .then(function(){ onSuccess(); }, function(err){ onError(err && err.text ? err.text : "Erreur"); });
}

/* ─── LEAD FORM ─── */
function renderLeadForm(container){
  const card = createEl("div","card");
  card.appendChild(createText("h2","","Votre diagnostic est pr\u00eat \u{1F389}"));
  const intro1 = createText("p","","Score global, alertes par section, points de vigilance et prochaines \u00e9tapes personnalis\u00e9es \u2014 tout est calcul\u00e9.");
  intro1.style.cssText = "font-size:0.9rem;color:var(--noir);line-height:1.6;margin-bottom:8px"; card.appendChild(intro1);
  const intro2 = createText("p","","Renseignez vos coordonn\u00e9es pour y acc\u00e9der. Ma\u00eetre Pinet pourra vous recontacter si votre profil le n\u00e9cessite.");
  intro2.style.cssText = "font-size:0.84rem;color:var(--gris-texte);margin-bottom:16px"; card.appendChild(intro2);

  const grid = createEl("div","form-grid");

  function addField(id, label, type, placeholder, required, fullWidth, isTextarea){
    const g = createEl("div","form-group" + (fullWidth ? " full-width" : ""));
    const lb = createEl("label","form-label"); lb.setAttribute("for","field_" + id);
    lb.textContent = label;
    if(required){ const s = createEl("span","required"); s.textContent = "*"; lb.appendChild(s); }
    g.appendChild(lb);
    var inp;
    if(isTextarea){
      inp = createEl("textarea","form-input form-textarea");
    } else {
      inp = createEl("input","form-input");
      inp.type = type || "text";
    }
    inp.id = "field_" + id; inp.name = id; inp.placeholder = placeholder || "";
    if(required) inp.required = true;
    g.appendChild(inp);
    g.appendChild(createEl("div","form-error"));
    grid.appendChild(g);
    return inp;
  }

  function addSelect(id, label, options, required, fullWidth){
    const g = createEl("div","form-group" + (fullWidth ? " full-width" : ""));
    const lb = createEl("label","form-label"); lb.setAttribute("for","field_" + id);
    lb.textContent = label;
    if(required){ const s = createEl("span","required"); s.textContent = "*"; lb.appendChild(s); }
    g.appendChild(lb);
    var sel = createEl("select","form-select");
    sel.id = "field_" + id; sel.name = id;
    if(required) sel.required = true;
    var optDef = createEl("option"); optDef.value = ""; optDef.textContent = "S\u00e9lectionnez\u2026"; optDef.disabled = true; optDef.selected = true; sel.appendChild(optDef);
    options.forEach(function(o){ var opt = createEl("option"); opt.value = o.value; opt.textContent = o.label; sel.appendChild(opt); });
    g.appendChild(sel);
    g.appendChild(createEl("div","form-error"));
    grid.appendChild(g);
    return sel;
  }

  function addSeparator(title){
    var sep = createEl("hr","form-separator"); grid.appendChild(sep);
    if(title){ var t = createText("div","form-section-title", title); grid.appendChild(t); }
  }

  /* --- Coordonn\u00e9es --- */
  var fNom = addField("nom","Nom","text","Votre nom", true, false);
  var fPrenom = addField("prenom","Pr\u00e9nom","text","Votre pr\u00e9nom", true, false);
  var fEmail = addField("email","Email","email","votre@email.com", true, false);
  var fTel = addField("telephone","T\u00e9l\u00e9phone","tel","06 12 34 56 78", true, false);

  /* --- Qualification prospect --- */
  addSeparator("Votre profil");
  var fProfil = addSelect("profil","Vous \u00eates\u2026",[
    { value: "futur_restaurateur", label: "Futur restaurateur (premi\u00e8re ouverture)" },
    { value: "restaurateur_en_activite", label: "Restaurateur en activit\u00e9 (d\u00e9veloppement)" },
    { value: "groupe_restauration", label: "Groupe de restauration" },
    { value: "investisseur", label: "Investisseur" },
    { value: "agent_immobilier", label: "Agent immobilier / Interm\u00e9diaire" },
    { value: "autre_profil", label: "Autre" }
  ], true, false);
  var fNbRestos = addSelect("nb_restos","Nombre de restaurants actuels",[
    { value: "0", label: "Aucun \u2014 c\u2019est mon premier" },
    { value: "1", label: "1 restaurant" },
    { value: "2_3", label: "2 \u00e0 3 restaurants" },
    { value: "4_plus", label: "4 restaurants ou plus" }
  ], true, false);

  /* --- Le local --- */
  addSeparator("Le local vis\u00e9");
  var fVille = addField("ville","Ville du local","text","Ex. : Lyon, Paris 11e\u2026", true, false);
  var fSurface = addField("surface","Surface du local (m\u00b2)","text","Ex. : 80", false, false);
  if(isCession()){
    var fPrix = addField("prix","Prix de cession propos\u00e9 (\u20ac)","text","Ex. : 150 000", false, false);
  }
  var fComment = addField("commentaire","Commentaire (facultatif)","text","D\u00e9crivez votre projet en quelques mots\u2026", false, true, true);

  card.appendChild(grid);

  /* Pre-fill info */
  if(isCession()){
    card.appendChild(buildSimulatorCTA());
  }

  var note = createText("div","form-note","\u{1F512} Vos donn\u00e9es sont transmises uniquement \u00e0 Ma\u00eetre Pinet et ne seront jamais partag\u00e9es.");
  card.appendChild(note);

  container.appendChild(card);

  var bg = createEl("div","btn-group");
  var btnSubmit = createText("button","btn btn-primary","Voir mon diagnostic \u2192");
  btnSubmit.addEventListener("click", function(){
    /* Validation */
    var valid = true;
    var fields = [
      { el: fNom, name: "nom" },
      { el: fPrenom, name: "prenom" },
      { el: fEmail, name: "email" },
      { el: fTel, name: "telephone" },
      { el: fProfil, name: "profil" },
      { el: fNbRestos, name: "nb_restos" },
      { el: fVille, name: "ville" }
    ];
    fields.forEach(function(f){
      var errDiv = f.el.parentElement.querySelector(".form-error");
      f.el.classList.remove("error");
      errDiv.textContent = "";
      if(!f.el.value.trim()){
        f.el.classList.add("error");
        errDiv.textContent = "Champ requis";
        valid = false;
      }
    });
    if(fEmail.value && !/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(fEmail.value)){
      fEmail.classList.add("error");
      fEmail.parentElement.querySelector(".form-error").textContent = "Email invalide";
      valid = false;
    }
    if(!valid){ showToast("Veuillez remplir les champs obligatoires"); return; }

    /* Collect data */
    var profilLabels = { futur_restaurateur:"Futur restaurateur (1\u00e8re ouverture)", restaurateur_en_activite:"Restaurateur en activit\u00e9", groupe_restauration:"Groupe de restauration", investisseur:"Investisseur", agent_immobilier:"Agent immobilier / Interm\u00e9diaire", autre_profil:"Autre" };
    var nbRestosLabels = { "0":"Aucun (premier restaurant)", "1":"1 restaurant", "2_3":"2 \u00e0 3 restaurants", "4_plus":"4 restaurants ou plus" };
    var formData = {
      nom: fNom.value.trim(),
      prenom: fPrenom.value.trim(),
      email: fEmail.value.trim(),
      telephone: fTel.value.trim(),
      profil: profilLabels[fProfil.value] || fProfil.value,
      profil_raw: fProfil.value,
      nb_restos: nbRestosLabels[fNbRestos.value] || fNbRestos.value,
      nb_restos_raw: fNbRestos.value,
      ville: fVille.value.trim(),
      surface: fSurface.value.trim(),
      prix: isCession() && fPrix ? fPrix.value.trim() : "",
      commentaire: fComment.value.trim()
    };

    /* Send via EmailJS */
    card.classList.add("form-sending");
    btnSubmit.textContent = "Envoi en cours\u2026";
    sendLeadData(formData, function(){
      showToast("Informations enregistr\u00e9es !");
      showScreen(currentIdx + 1);
    }, function(err){
      card.classList.remove("form-sending");
      btnSubmit.textContent = "Voir mon diagnostic \u2192";
      showToast("Erreur d\u2019envoi \u2014 v\u00e9rifiez votre connexion");
      console.error("EmailJS error:", err);
      /* Allow access anyway after error */
      setTimeout(function(){ showScreen(currentIdx + 1); }, 1500);
    });
  });
  bg.appendChild(btnSubmit);
  var btnBack = createText("button","btn btn-secondary","\u2190 Revenir aux questions");
  btnBack.addEventListener("click", function(){ showScreen(currentIdx - 1); });
  bg.appendChild(btnBack);
  container.appendChild(bg);
}

/* ─── RESULTS ─── */
function renderResult(container){
  const sectionScores = {}; const alerts = []; let totalScore = 0, totalMax = 0;
  sections.forEach(function(sec){ sectionScores[sec.id] = { score: 0, max: 0, name: sec.name, icon: sec.icon }; });

  const activeQs = getActiveQuestions(); const unanswered = []; const unknowns = [];
  activeQs.forEach(function(item){
    const q = item.question, sec = item.section, a = state.answers[q.id];
    if(a){
      sectionScores[sec.id].score += a.score; sectionScores[sec.id].max += MAX_SCORE;
      totalScore += a.score; totalMax += MAX_SCORE;
      if(q.alert_if && q.alert_msg && q.alert_if.indexOf(a.value) !== -1) alerts.push({ section: sec.name, msg: q.alert_msg, level: "red" });
      if(a.value === "inconnu") unknowns.push(item);
    } else { unanswered.push(item); }
  });

  const globalPct = totalMax > 0 ? Math.round((totalScore / totalMax) * 100) : 0;
  const globalColor = getScoreColor(globalPct);

  /* Profile summary */
  renderProfileTags(container);

  /* Score */
  const scoreCard = createEl("div","card");
  const gd = createEl("div","global-score");
  const sn = createText("div","score-number", globalPct + "%"); sn.style.color = globalColor; gd.appendChild(sn);
  gd.appendChild(createText("div","score-label-text","Score global de visite")); scoreCard.appendChild(gd);

  let interp;
  if(globalPct >= 70) interp = "Profil globalement favorable. Restez vigilant sur les points signal\u00e9s.";
  else if(globalPct >= 40) interp = "Plusieurs points n\u00e9cessitent attention. Approfondissez avant de vous engager.";
  else interp = "Risques importants identifi\u00e9s. Accompagnement professionnel recommand\u00e9.";
  const ip = createText("p","", interp); ip.style.cssText = "text-align:center;font-size:0.85rem;color:var(--gris-texte)"; scoreCard.appendChild(ip);

  if(unanswered.length > 0){
    const sn2 = createText("p","", unanswered.length + " question(s) non r\u00e9pondue(s) \u2014 non comptabilis\u00e9e(s) dans le score.");
    sn2.style.cssText = "text-align:center;font-size:0.78rem;color:var(--orange-warning);margin-top:8px"; scoreCard.appendChild(sn2);
  }
  container.appendChild(scoreCard);

  /* Section detail */
  const dc = createEl("div","card"); dc.appendChild(createText("h3","","D\u00e9tail par section"));
  Object.keys(sectionScores).forEach(function(k){
    const s = sectionScores[k]; if(s.max === 0) return;
    const pct = Math.round((s.score / s.max) * 100); const col = getScoreColor(pct);
    const sd = createEl("div","score-section");
    const ld = createEl("div","score-label");
    ld.appendChild(createText("span","score-name", s.icon + " " + s.name));
    const vs = createText("span","score-value", pct + "%"); vs.style.color = col; ld.appendChild(vs); sd.appendChild(ld);
    const bd = createEl("div","score-bar"); const fd = createEl("div","score-bar-fill");
    fd.style.background = col; fd.setAttribute("data-width", pct); bd.appendChild(fd); sd.appendChild(bd); dc.appendChild(sd);
  });
  container.appendChild(dc);
  setTimeout(function(){ document.querySelectorAll(".score-bar-fill").forEach(function(b){ b.style.width = b.getAttribute("data-width") + "%"; }); }, BAR_ANIM_DELAY);

  /* Alerts */
  if(alerts.length > 0){
    const ac = createEl("div","card"); ac.appendChild(createText("h3","","Points de vigilance"));
    alerts.forEach(function(a){
      const it = createEl("div","alert-item"); it.appendChild(createEl("div","alert-dot " + a.level));
      const td = createEl("div"); td.appendChild(createText("strong","", a.section));
      td.appendChild(document.createTextNode(" \u2014 " + a.msg)); it.appendChild(td); ac.appendChild(it);
    });
    container.appendChild(ac);
  }

  /* Unknowns */
  if(unknowns.length > 0){
    const uc = createEl("div","card"); uc.appendChild(createText("h3","","Points \u00e0 v\u00e9rifier (\u00ab\u00a0Je ne sais pas\u00a0\u00bb)"));
    uc.appendChild(createText("p","","Ces points m\u00e9ritent une v\u00e9rification :"));
    unknowns.forEach(function(item){
      const row = createEl("div","inconnu-item"); row.appendChild(createEl("div","alert-dot orange"));
      row.appendChild(createText("div","", item.section.icon + " " + item.question.text)); uc.appendChild(row);
    });
    container.appendChild(uc);
  }

  /* Unanswered */
  if(unanswered.length > 0){
    const uc2 = createEl("div","card"); uc2.appendChild(createText("h3","","Questions non r\u00e9pondues"));
    uc2.appendChild(createText("p","","Non comptabilis\u00e9es dans le score :"));
    const list = createEl("ul","result-checklist");
    unanswered.forEach(function(item){
      const li = createEl("li"); li.appendChild(createText("span","check-icon","\u25CB"));
      li.appendChild(createText("span","", item.section.icon + " " + item.question.text)); list.appendChild(li);
    });
    uc2.appendChild(list); container.appendChild(uc2);
  }

  /* Next steps */
  const nc = createEl("div","card"); nc.appendChild(createText("h3","","Prochaines \u00e9tapes recommand\u00e9es"));
  const sl = createEl("ul","result-checklist");
  let steps = [];
  if(isCession()) steps.push("Faites analyser le bail par un avocat","Faites auditer les bilans par un expert-comptable","V\u00e9rifiez le nantissement aupr\u00e8s du greffe");
  else steps.push("Faites analyser le bail commercial par un avocat");
  steps.push("Consultez un architecte ou ma\u00eetre d\u2019\u0153uvre pour les travaux","V\u00e9rifiez la conformit\u00e9 ERP aupr\u00e8s de la mairie ou pr\u00e9fecture");
  if(needsExtraction()) steps.push("Faites r\u00e9aliser un diagnostic extraction par un professionnel agr\u00e9\u00e9");
  if(isGrandeCuisine()) steps.push("Faites v\u00e9rifier la conformit\u00e9 \u00abgrande cuisine\u00bb (extinction automatique, arr\u00eat d\u2019urgence)");
  if(isFranchise()) steps.push("Faites v\u00e9rifier la compatibilit\u00e9 bail / contrat de franchise par un avocat","Analysez le DIP avant toute signature");
  steps.push("Pr\u00e9parez votre business plan avec le co\u00fbt r\u00e9el des travaux et mises en conformit\u00e9");
  if(isCession()) steps.push("Estimez la valeur du fonds avec notre simulateur de valorisation restaurant");
  steps.forEach(function(s){ const li = createEl("li"); li.appendChild(createText("span","check-icon","\u2192")); li.appendChild(createText("span","",s)); sl.appendChild(li); });
  nc.appendChild(sl);
  if(isCession()){
    const simLink = createEl("div","info-box"); simLink.style.marginTop = "16px";
    simLink.appendChild(createText("span","info-title","Simulateur de valorisation restaurant"));
    const linkText = createText("span","","Estimez le vrai prix du fonds de commerce que vous envisagez de reprendre : ");
    simLink.appendChild(linkText);
    const a = createEl("a"); a.textContent = "Acc\u00e9der au simulateur"; a.href = "https://www.louispinetavocat.fr/contenus/simulateur-valorisation-restaurant"; a.target = "_blank"; a.rel = "noopener";
    a.style.cssText = "color:var(--noir);font-weight:600;text-decoration:underline";
    simLink.appendChild(a);
    nc.appendChild(simLink);
  }
  container.appendChild(nc);

  /* Contact CTA */
  const ctaCard = createEl("div","contact-cta-card");
  ctaCard.appendChild(createText("h3","","Besoin d\u2019un accompagnement ?"));
  const ctaText = globalPct < 70
    ? "Votre audit r\u00e9v\u00e8le des points sensibles. Ma\u00eetre Pinet peut s\u00e9curiser votre projet avant toute signature."
    : "Ma\u00eetre Pinet v\u00e9rifie le bail, les actes et la conformit\u00e9 pour s\u00e9curiser votre acquisition.";
  ctaCard.appendChild(createText("p","", ctaText));
  const ctaBtns = createEl("div","contact-btns");
  const waLink = createEl("a","btn-whatsapp");
  waLink.href = "https://wa.me/33686259294?text=Bonjour%20Ma%C3%AEtre%2C%20je%20viens%20de%20r%C3%A9aliser%20l%27audit%20de%20visite%20restaurant%20et%20j%27aimerais%20en%20discuter.";
  waLink.target = "_blank"; waLink.rel = "noopener";
  waLink.textContent = "\u{1F4AC} WhatsApp"; ctaBtns.appendChild(waLink);
  const contactLink = createEl("a","btn-contact");
  contactLink.href = "https://www.louispinetavocat.fr/contactez-moi";
  contactLink.target = "_blank"; contactLink.rel = "noopener";
  contactLink.textContent = "\u2709\uFE0F Contactez-moi"; ctaBtns.appendChild(contactLink);
  ctaCard.appendChild(ctaBtns);
  container.appendChild(ctaCard);

  /* Disclaimer */
  const disc = createEl("div","disclaimer");
  disc.appendChild(createText("strong","","Avertissement : "));
  disc.appendChild(document.createTextNode("Ce diagnostic est un outil d\u2019aide \u00e0 la d\u00e9cision \u00e9dit\u00e9 par Ma\u00eetre Louis Pinet. Il ne constitue pas un conseil juridique personnalis\u00e9."));
  container.appendChild(disc);

  /* Buttons */
  const bg = createEl("div","btn-group");
  const br = createText("button","btn btn-secondary","Recommencer l\u2019audit");
  br.addEventListener("click", function(){ renderApp(); }); bg.appendChild(br);
  const bp = createText("button","btn btn-dark","Imprimer le r\u00e9sultat");
  bp.addEventListener("click", function(){ window.print(); }); bg.appendChild(bp);
  container.appendChild(bg);
}

renderApp();
})();
</script>
<script src="https://cdn.jsdelivr.net/npm/@emailjs/browser@4/dist/email.min.js"></script>
</body>
</html># assitant-diag-resto

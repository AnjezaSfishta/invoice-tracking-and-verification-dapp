# Arkitektura e sistemit dhe dizajni i API-së

## Arkitektura

```text
┌──────────────────────┐      ┌─────────────────────────────┐
│ React dApp           │─────▶│ MetaMask                    │
│ pesë pamje           │      │ nënshkrimi i transaksioneve │
└──────────┬───────────┘      └──────────────┬──────────────┘
           │ ethers.js                       │
           │ leximet/ngarkimet                │ transaksionet
           ▼                                  ▼
┌──────────────────────┐      ┌─────────────────────────────┐
│ API REST             │─────▶│ Pinata / IPFS               │
│ Node.js / Express    │ upload│ PDF dhe JSON metadata       │
│ indeksues në memorie │      └─────────────────────────────┘
└──────────┬───────────┘
           │ evente
           ▼
┌─────────────────────────────┐
│ Hardhat / Sepolia           │
│ InvoiceRegistry             │
│ InvoiceWorkflow             │
└─────────────────────────────┘

PDF dhe metadata: IPFS (publike)
Hash SHA-256, palët dhe historiku i statusit: në blockchain
```

## Përbërësit

| Përbërësi | Përgjegjësia |
|---|---|
| `InvoiceRegistry` | Regjistron faturat, adresat e palëve, hash-in, shumën, monedhën dhe URI-në opsionale. |
| `InvoiceWorkflow` | Zbaton kalimet e statusit dhe ruan historikun me autorin, kohën dhe shënimin. |
| Frontend React | Ofron pamjet e përdoruesit, llogarit hash-in e PDF-së dhe lexon kontratat. |
| MetaMask | Jep llogarinë aktive dhe nënshkruan transaksionet e përdoruesit. |
| API Express | Indekson eventet, pranon PDF-në dhe publikon PDF/metadata në Pinata/IPFS. |
| Hardhat / Sepolia | Rrjetet për zhvillim lokal dhe demonstrim testues. |

## API-ja

Backend-i përdor Express dhe ethers.js për të lexuar eventet në blloqe dhe për t’i ofruar përmes endpoint-eve:

| Metoda | Endpoint | Qëllimi |
|---|---|---|
| `GET` | `/api/health` | Gjendja e API-së dhe e lidhjes me rrjetin. |
| `GET` | `/api/invoices` | Faturat e indeksuara nga eventet e regjistrimit. |
| `GET` | `/api/events` | Eventet e ndryshimit të statusit. |
| `GET` | `/api/invoices/:id/history` | Historiku i një fature sipas identifikuesit. |
| `GET` | `/api/addresses/:address/invoices` | Faturat ku adresa është lëshues ose pranues. |

Indeksi aktual mbahet në memorie dhe rindërtohet nga eventet pas rinisjes së backend-it. Për këtë arsye API-ja është indeksues ndihmës; gjendja autoritative ruhet në blockchain.

## Struktura e repository-t

```text
contracts/                 Kontratat Solidity
test/                      Testet automatike të kontratave
scripts/deploy.js          Deploy lokal dhe Sepolia
scripts/seed-demo.js       Të dhënat lokale demonstrative
frontend/                  Aplikacioni React dhe konfigurimi Vite
backend/                   API REST dhe indeksuesi i eventeve
demo/documents/             PDF-të e krijuara për demonstrim lokal
deployments/                Adresat e kontratave pas deploy-it
README.md                   Dokumentacioni teknik
```

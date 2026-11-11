# Dizajni i kontratave dhe modeli i të dhënave

## Kontratat inteligjente

`InvoiceRegistry` dhe `InvoiceWorkflow` implementohen në Solidity 0.8.24. Regjistri thërret workflow-n kur krijohet faturë, ndërsa workflow-ja lexon të dhënat e palëve nga regjistri para se të lejojë ndryshimin e statusit.

Kontratat emetojnë nëntë lloje eventesh: `InvoiceRegistered`, `StatusChanged`, `AuditorUpdated`, `WorkflowConfigured`, `InvoiceSent`, `InvoiceAccepted`, `InvoiceDisputed`, `InvoicePaid` dhe `InvoiceCancelled`.

## Modeli i të dhënave

Regjistrimi i faturës përmban:

| Fusha | Përshkrimi |
|---|---|
| `id` | Identifikues numerik, unik për kontratën. |
| `issuer` | Adresa blockchain e biznesit lëshues. |
| `recipient` | Adresa blockchain e klientit. |
| `documentHash` | Hash 32-bajtësh SHA-256 i PDF-së. |
| `amount` | Shuma në njësi me dy shifra dhjetore, e ruajtur si numër i plotë. |
| `currency` | Kodi i monedhës, për shembull `EUR`. |
| `metadataURI` | URI IPFS e JSON metadata-s që përmban URI-në e PDF-së dhe hash-in SHA-256. |
| `createdAt` | Koha e regjistrimit në blockchain. |

Për çdo ndryshim statusi regjistrohen statusi i ri, adresa që kreu veprimin, koha dhe një shënim.

## Modeli i procesit

Statuset janë `Registered`, `Sent`, `Accepted`, `Disputed`, `Paid` dhe `Cancelled`.

```text
Registered ──biznesi──▶ Sent ──klienti──▶ Accepted ──klienti──▶ Paid
                           │
                           └──klienti──▶ Disputed ──klienti──▶ Accepted

Registered / Sent / Disputed ──biznesi ose klienti──▶ Cancelled
```

Pagesa është status demonstrues: kontratat nuk transferojnë fonde. Anulimi lejohet vetëm para statusit `Paid`.

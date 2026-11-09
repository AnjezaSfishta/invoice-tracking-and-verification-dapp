# Use cases dhe kërkesat funksionale

## Kërkesat dhe kufiri i projektit

Në fushëveprimin e zgjidhjes përfshihen:

1. Dy kontrata inteligjente të ndërlidhura.
2. Role të dallueshme dhe validime të autorizimit.
3. Gjashtë statuse të faturës dhe historik i ndryshimeve.
4. Evente blockchain-i për regjistrimin dhe veprimet e procesit.
5. Pesë pamje të dApp-it: përmbledhja, regjistrimi, historia, veprimet dhe verifikimi.
6. API REST për indeksimin dhe leximin e eventeve.
7. Teste automatike, përfshirë kërkesa të paautorizuara dhe kalime të pavlefshme.

PDF-ja e faturës dhe metadata ruhen off-chain në IPFS përmes Pinata. Backend-i mban JWT-në e Pinata-s dhe ngarkon PDF-në bashkë me një JSON që përmban CID-në dhe hash-in SHA-256; blockchain-i ruan hash-in dhe URI-në e metadata-s. Përmbajtja e IPFS është publike dhe nuk duhet përdorur për dokumente konfidenciale apo të dhëna personale.

## Rolet

- **Administratori:** deployer-i i kontratës së regjistrit; konfiguron lidhjen me workflow-n dhe autorizon auditorët.
- **Biznesi / lëshuesi:** regjistron faturën dhe e dërgon te klienti.
- **Klienti / pranuesi:** pranon faturën, hap kontestim ose shënon statusin si të paguar.
- **Auditori:** mund të autorizohet nga administratori. Regjistri është publik, prandaj auditori nuk ka privilegj të veçantë për lexim dhe nuk mund të ndryshojë statusin.

Thirrjet për ndryshimin e statusit kontrollojnë në kontratë adresën e palës përkatëse. Asnjë rol nuk mund të ndryshojë një faturë që nuk i përket.

## Use cases kryesore

| Nr. | Use case | Aktori | Rezultati |
|---|---|---|---|
| UC1 | Regjistrimi i faturës (hash SHA-256 + metadata IPFS) | Biznesi | Fatura regjistrohet me statusin `Registered` |
| UC2 | Dërgimi i faturës | Biznesi | Statusi `Sent` |
| UC3 | Pranimi i faturës | Klienti | Statusi `Accepted` |
| UC4 | Kontestimi i faturës me arsye | Klienti | Statusi `Disputed` |
| UC5 | Pagesa (demonstruese) | Klienti | Statusi `Paid` |
| UC6 | Anulimi i faturës | Biznesi ose klienti | Statusi `Cancelled` |
| UC7 | Verifikimi i PDF-së me hash | Çdokush / auditori | Përputhje ose dështim nëse dokumenti është ndryshuar |
| UC8 | Shikimi i historisë së statusit | Çdokush / auditori | Histori e auditueshme |

## Kriteret e pranimit

- Fatura e ndryshuar dështon në verifikim.
- Vetëm palët e faturës ndryshojnë statusin.
- Historia e statusit mbetet e auditueshme.

<properties 
   pageTitle="Elenco di aggiornamenti applicati al sistema operativo guest di Azure | Azure" 
   description="Elenco di aggiornamenti applicati alle versioni del sistema operativo guest" 
   services="cloud-services" 
   documentationCenter="na" 
   authors="Thraka" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd" 
   ms.date="03/18/2015"
   ms.author="adegeo"/>

# Sistema operativo guest di Azure 

La tabella seguente mostra gli aggiornamenti di Microsoft Security Response Center (MSRC) applicati al sistema operativo guest di Azure. Eseguire ricerche in questo articolo per determinare se un aggiornamento specifico è applicabile al sistema operativo guest in uso. Gli aggiornamenti vengono sempre riportati per la [famiglia][family-explain] specifica in cui sono stati introdotti. 


## Sistema operativo guest di marzo

| ID bollettino | Articolo della Knowledge Base padre   | Descrizione delle vulnerabilità                                                   | Sistema operativo guest         | Data della prima introduzione |
| ----------- | ------------------- | --------------------------------------------------------------------------- | ---------------- | --------------------- |
| MS15-018    | [3032359][3032359] | Aggiornamento cumulativo della sicurezza per Internet Explorer                             | 4.18, 3.25, 2.37 | N/D |
| MS15-019    | [3040297][3040297] | La vulnerabilità nel motore di script VBScript potrebbe consentire l'esecuzione di codice in modalità remota | 4.18, 3.25, 2.37 | N/D |
| MS15-020    | [3041836][3041836] | Le vulnerabilità in Windows potrebbero consentire l'esecuzione di codice in modalità remota                 | 4.18, 3.25, 2.37 | N/D |
| MS15-021    | [3032323][3032323] | Le vulnerabilità nel driver di caratteri di Adobe potrebbero consentire l'esecuzione di codice in modalità remota          | 4.18, 3.25, 2.37 | N/D |
| MS15-023    | [3034344][3034344] | Possibile elevazione di privilegi a causa di vulnerabilità del driver in modalità kernel     | 4.18, 3.25, 2.37 | N/D |
| MS15-024    | [3035132][3035132] | La vulnerabilità nell'elaborazione di PNG potrebbe consentire la diffusione di informazioni           | 4.18, 3.25, 2.37 | N/D |
| MS15-025    | [3038680][3038680] | Le vulnerabilità nel kernel di Windows potrebbero consentire l'elevazione dei privilegi         | 4.18, 3.25, 2.37 | N/D |
| MS15-027    | [3002657][3002657] | Possibili rischi di spoofing a causa di vulnerabilità in NETLOGON                               | 4.18, 3.25, 2.37 | N/D |
| MS15-029    | [3035126][3035126] | La vulnerabilità nel componente Windows Photo Decoder potrebbe consentire la divulgazione di informazioni | 4.18, 3.25, 2.37 | N/D |

> [AZURE.NOTE] Il bollettino MS15-031 potrebbe apparire come non installato. Non è tuttavia applicabile a questa versione del sistema operativo guest.

## Sistema operativo guest di febbraio

| ID bollettino | Articolo della Knowledge Base padre   | Descrizione delle vulnerabilità                                                   | Sistema operativo guest         | Data della prima introduzione |
| ----------- | ------------------- | --------------------------------------------------------------------------- | ---------------- | --------------------- |
| MS15-009    | [3034682][3034682] | Aggiornamento della sicurezza per Internet Explorer                                                   | 4.17, 3.24, 2.36 | N/D |
| MS15-010    | [3036220][3036220] | Le vulnerabilità nei driver in modalità kernel di Windows potrebbero consentire l'esecuzione di codice in modalità remota        | 4.17, 3.24, 2.36 | N/D |
| MS15-011    | [3000483][3000483] | La vulnerabilità in Criteri di gruppo potrebbe consentire l'esecuzione di codice in modalità remota                         | 4.17, 3.24, 2.36 | N/D |
| MS15-014    | [3004361][3004361] | La vulnerabilità in SMB potrebbe consentire il bypass della funzionalità di sicurezza                                | 4.17, 3.24, 2.36 | N/D |
| MS15-015    | [3031432][3031432] | La vulnerabilità in Microsoft Windows potrebbe consentire l'elevazione dei privilegi                   | 4.17, 3.24, 2.36 | N/D |
| MS15-016    | [3029944][3029944] | La vulnerabilità nel componente grafico Microsoft potrebbe consentire la divulgazione di informazioni        | 4.17, 3.24, 2.36 | N/D |
| N/D         | [3004375][3004375] | Aggiornamento per migliorare il controllo della riga di comando di Windows<p/>**Nota**: è installato, ma la chiave del Registro di sistema per abilitarlo è disattivata. | 4.17, 3.24, 2.36 | N/D |
 

## Sistema operativo guest di gennaio

| ID bollettino | Articolo della Knowledge Base padre   | Descrizione delle vulnerabilità                                                   | Sistema operativo guest         | Data della prima introduzione |
| ----------- | ------------------- | --------------------------------------------------------------------------- | ---------------- | --------------------- |
| MS15-001    | [3023266][3023266]  | Possibile elevazione di privilegi a causa di una vulnerabilità in Windows AppCompatCache  | 4.16, 3.23, 2.35 | 19 gen 2015           |
| MS15-002    | [3020393][3020393]  | La vulnerabilità nel servizio Telnet di Windows potrebbe causare l'esecuzione di codice in modalità remota   | 4.16, 3.23, 2.35 | 19 gen 2015           |
| MS15-003    | [3021674][3021674]  | Possibile elevazione dei privilegi a causa di una vulnerabilità nel servizio profili utente di Windows   | 4.16, 3.23, 2.35 | 19 gen 2015    |
| MS15-004    | [3019978][3019978]  | Possibile elevazione di privilegi a causa di una vulnerabilità in Componenti di Windows      | 4.16, 3.23, 2.35 | 19 gen 2015           |
| MS15-005    | [3022777][3022777]  | La vulnerabilità in NLA potrebbe consentire il bypass della funzionalità di sicurezza                    | 4.16, 3.23, 2.35 | 19 gen 2015           |
| MS15-006    | [3004365][3004365]  | La vulnerabilità in Segnalazione errori Windows potrebbe consentire il bypass della funzionalità di sicurezza | 4.16, 3.23, 2.35 | 19 gen 2015          |
| MS15-007    | [3014029][3014029]  | La vulnerabilità nel protocollo RADIUS del server dei criteri di rete potrebbe causare un attacco Denial of Service | 4.16, 3.23, 2.35 | 19 gen 2015           |
| MS15-008    | [3019215][3019215]  | La vulnerabilità nel driver in modalità kernel di Windows potrebbe consentire l'elevazione dei privilegi | 4.16, 3.23, 2.35 | 19 gen 2015        |
| MS14-080    | [3008923][3008923]  | Aggiornamento cumulativo della sicurezza per Internet Explorer                            | 4.16, 3.23, 2.35 | 19 gen 2015           |
| MS15-002    | [3020393][3020393]  | La vulnerabilità nel servizio Telnet di Windows potrebbe causare l'esecuzione di codice in modalità remota   | 4.16, 3.23, 2.35 | 19 gen 2015           |
| MS15-002    | [3020393][3020393]  | La vulnerabilità nel servizio Telnet di Windows potrebbe causare l'esecuzione di codice in modalità remota   | 4.16, 3.23, 2.35 | 19 gen 2015           |
 
## Sistema operativo guest di dicembre

| ID bollettino | Articolo della Knowledge Base padre   | Descrizione delle vulnerabilità                                                   | Sistema operativo guest         | Data della prima introduzione |
| ----------- | ------------------- | --------------------------------------------------------------------------- | ---------------- | --------------------- |
| N/D         | [3013776][3013776]  | Il sistema si blocca quando si usa un account di dominio per avviare un'applicazione | 4.15, 3.22, 2.34 | 13 gen 2015 | 
| N/D         | [3013043][3013043]  | I dati del file system vengono danneggiati in un computer basato su Windows con più di un nodo NUMA | 4.15, 3.22, 2.34 | 13 gen 2015 |
| N/D         | [3012712][3012712]  | I nuovi blocchi di dati non vengono inizializzati correttamente quando viene espanso un VHD differenze | 4.15, 3.22, 2.34 | 13 gen 201 |
| N/D         | [3004905][3004905]  | Miglioramento di Windows Hyper-V per macchine virtuali Linux con file system di dimensione superiore ai 2 TB | 4.15, 3.22, 2.34 | 13 gen 2015 |
| N/D         | [3004394][3004394]  | Aggiornamento di dicembre 2014 per il programma Windows Root Certificate in Windows | 4.15, 3.22, 2.34 | 13 gen 2015 |
| N/D         | [2999323][2999323]  | Il testo dell'evento ID 17 è cambiato | 4.15, 3.22, 2.34 | 13 gen 2015 |
| N/D         | [3013488][3013488]  | È necessario molto tempo per reimpostare il server WSUS quando si importano file CSA in Windows Server 2012 R2 o Windows Server 2012 | 4.15, 3.22, 2.34 | 13 gen 2015 |
| N/D         | [3012325][3012325]  | Aggiornamento delle voci dei database APN di Windows per gli operatori di telefonia mobile DIGI, Vodafone e Telekom in Windows 8.1 e Windows 8 | 4.15, 3.22, 2.34 | 13 gen 2015 |
| N/D         | [3007054][3007054]  | L'opzione di stampa protetta da PIN viene sempre visualizzata quando si stampa un documento in un'applicazione di Windows Store in Windows | 4.15, 3.22, 2.34 | 13 gen 2015 |
| N/D         | [2999802][2999802]  | In Windows vengono stampate linee a tinta unita invece che linee tratteggiate | 4.15, 3.22, 2.34 | 13 gen 2015 |
| N/D         | [2896881][2896881]  | È necessario molto tempo per l'accesso quando si usa il comando VBScript AddPrinterConnection per eseguire il mapping delle stampanti per gli utenti durante il processo di accesso in Windows | 4.15, 3.22, 2.34 | 13 gen 2015 |




[3034682]: http://support.microsoft.com/kb/3034682
[3036220]: http://support.microsoft.com/kb/3036220
[3000483]: http://support.microsoft.com/kb/3000483
[3004361]: http://support.microsoft.com/kb/3004361
[3031432]: http://support.microsoft.com/kb/3031432
[3029944]: http://support.microsoft.com/kb/3029944
[3004375]: http://support.microsoft.com/kb/3004375

[3023266]: http://support.microsoft.com/kb/3023266
[3020393]: http://support.microsoft.com/kb/3020393
[3021674]: http://support.microsoft.com/kb/3021674
[3019978]: http://support.microsoft.com/kb/3019978
[3022777]: http://support.microsoft.com/kb/3022777
[3004365]: http://support.microsoft.com/kb/3004365
[3014029]: http://support.microsoft.com/kb/3014029
[3019215]: http://support.microsoft.com/kb/3019215
[3008923]: http://support.microsoft.com/kb/3008923
[3020393]: http://support.microsoft.com/kb/3020393

[3013776]: http://support.microsoft.com/kb/3013776
[3013043]: http://support.microsoft.com/kb/3013043
[3012712]: http://support.microsoft.com/kb/3012712
[3004905]: http://support.microsoft.com/kb/3004905
[3004394]: http://support.microsoft.com/kb/3004394
[2999323]: http://support.microsoft.com/kb/2999323
[3013488]: http://support.microsoft.com/kb/3013488
[3012325]: http://support.microsoft.com/kb/3012325
[3007054]: http://support.microsoft.com/kb/3007054
[2999802]: http://support.microsoft.com/kb/2999802
[2896881]: http://support.microsoft.com/kb/2896881

[3032359]: http://support.microsoft.com/kb/3032359
[3040297]: http://support.microsoft.com/kb/3040297
[3041836]: http://support.microsoft.com/kb/3041836
[3032323]: http://support.microsoft.com/kb/3032323
[3034344]: http://support.microsoft.com/kb/3034344
[3035132]: http://support.microsoft.com/kb/3035132
[3038680]: http://support.microsoft.com/kb/3038680
[3002657]: http://support.microsoft.com/kb/3002657
[3035126]: http://support.microsoft.com/kb/3035126


[archivio]: https://msdn.microsoft.com/library/azure/dn391773.aspx
[family-explain]: cloud-services-guestos-update-matrix.md#guest-os-family-version-and-release-explanation


<!--HONumber=52-->
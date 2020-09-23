---
title: Riferimento allo schema di normalizzazione dei dati di Azure Sentinel | Microsoft Docs
description: Questo articolo mostra lo schema di normalizzazione dei dati di Sentinel di Azure.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 09/08/2020
ms.author: yelevin
ms.openlocfilehash: eb1752ea66f2cbebf6a653705b5a760e8e268240
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90937603"
---
# <a name="azure-sentinel-data-normalization-schema-reference"></a>Riferimento allo schema di normalizzazione dei dati di Sentinel di Azure

## <a name="terminology"></a>Terminologia

Negli schemi di Sentinel viene usata la terminologia seguente:

| Termine | Definizione |
| ---- | ---------- |
| Dispositivo di report | Il sistema invia i record ad Azure Sentinel. Potrebbe non essere il sistema oggetto del record. |
| Record | Unità di dati inviata dal dispositivo per la creazione di report. Questa operazione viene spesso definita "log", "Event" o "Alert", ma non necessariamente uno di questi. |
|

## <a name="data-types-and-formats"></a>Tipi di dati e formati

I valori devono essere normalizzati in base alle linee guida riportate di seguito. Questa operazione è obbligatoria per i campi normalizzati ed è consigliata per gli altri campi. 

| Tipo di dati | Tipo fisico | Formato e valore |
| --------- | ------------- | ---------------- |
| **Data/ora** | A seconda dell'utilizzo della funzionalità del metodo di inserimento in priorità decrescente:<ul><li>Log Analytics tipo DateTime incorporato</li><li>Campo integer con Log Analytics rappresentazione numerica DateTime</li><li>Campo stringa con Log Analytics rappresentazione numerica DateTime</li></ul> | Rappresentazione Log Analytics DateTime. <br></br>La rappresentazione della data & ora Log Analytics è simile, ma è diversa dalla rappresentazione dell'ora UNIX. Vedere queste linee guida per la conversione. <br></br>La data & tempo deve essere regolata dal fuso orario. |
| **Indirizzo MAC** | Stringa | Notazione esadecimale con due punti |
| **Indirizzo IP** | Indirizzo IP | Lo schema non dispone di indirizzi IPv4 e IPv6 distinti. Qualsiasi campo dell'indirizzo IP può includere un indirizzo IPv4 o un indirizzo IPv6:<ul><li>IPv4 in una notazione decimale punto</li><li>IPv6 in 8 notazione hextets, che consente i moduli brevi descritti qui.</li></ul> |
| **Utente** | Stringa | Sono disponibili i 3 campi utente seguenti:<ul><li>Nome utente</li><li>UPN dell'utente</li><li>Dominio utente</li></ul> |
| **ID utente** | Stringa | Sono attualmente supportati i due ID utente seguenti:<ul><li>SID utente</li><li>ID di Azure Active Directory</li></ul> |
| **Dispositivo** | Stringa | Sono supportate le 3 colonne del dispositivo/host seguenti:<ul><li>ID</li><li>Nome</li><li>Nome di dominio completo (FQDN)</li></ul> |
| **Paese** | Stringa | Stringa che usa ISO 3166-1 in base a questa priorità:<ul><li>Codici alfa-2 (ad esempio, US per la Stati Uniti)</li><li>Codici alfa-3 (ad esempio USA per la Stati Uniti)</li><li>Nome breve</li></ul> |
| **Area** | Stringa | Nome della sottodivisione paese con ISO 3166-2 |
| **Città** | Stringa | |
| **Longitudine** | Double | Rappresentazione coordinata ISO 6709 (decimale con segno) |
| **Latitudine** | Double | Rappresentazione coordinata ISO 6709 (decimale con segno) |
| **Algoritmo hash** | Stringa | Sono supportate le 4 colonne hash seguenti:<ul><li>MD5</li><li>SHA1</li><li>SHA256</li><li>SHA512</li></ul> |
| **Tipo di file** | Stringa | Tipo di file:<ul><li>Estensione</li><li>Classe</li><li>NamedType</li></ul> |
| 

## <a name="network-sessions-table-schema"></a>Schema della tabella delle sessioni di rete

Di seguito è riportato lo schema della tabella delle sessioni di rete con versione 1.0.0

| Nome campo | Tipo valore | Esempio | Descrizione | Entità OSSEM associate |
|-|-|-|-|-|
| EventType | Stringa | Traffico | Tipo di evento raccolto | Evento |
| EventSubType | Stringa | Authentication | Descrizione aggiuntiva del tipo, se applicabile | Evento |
| EventCount | Integer  | 10 | Il numero di eventi aggregati, se applicabile. | Evento |
| EventEndTime | Data/ora | Vedere "tipi di dati" | Ora di fine dell'evento | Evento |
| EventMessage | string |  accesso negato | Un messaggio generale o una descrizione, inclusa o generata dal record | Evento |
| DvcIpAddr | Indirizzo IP |  23.21.23.34 | Indirizzo IP del dispositivo che genera il record | Dispositivo<br>IP |
| DvcMacAddr | Stringa | 06:10:9F: EB: 8F: 14 | Indirizzo MAC dell'interfaccia di rete del dispositivo di report da cui è stato inviato l'evento. | Dispositivo<br>Mac |
| DvcHostname | Nome dispositivo (stringa) | syslogserver1.contoso.com | Nome del dispositivo che genera il messaggio. | Dispositivo |
| EventProduct | Stringa | OfficeSharepoint | Prodotto che genera l'evento. | Evento |
| EventProductVersion | string | 9.0 |  Versione del prodotto che genera l'evento. | Evento |
| EventResourceId | ID dispositivo (stringa) | /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /syslogserver1 | ID risorsa del dispositivo che genera il messaggio. | Evento |
| EventReportUrl | Stringa | https://192.168.1.1/repoerts/ae3-56.htm | Collegamento al report completo creato dal dispositivo di report | Evento |
| EventVendor | Stringa | Microsoft | Fornitore del prodotto che genera l'evento. | Evento |
| EventResult | Multivalore: Success, partial, Failure, [Empty] (String) | Operazione completata | Risultato riportato per l'attività. Valore vuoto quando non applicabile. | Evento |
| EventResultDetails | Stringa | Password errata | Motivo o dettagli del risultato riportato in EventResult | Evento |
| EventSchemaVersion | Real | 0,1 | Versione dello schema di Sentinel di Azure. Attualmente 0,1. | Evento |
| EventSeverity | Stringa | Bassa | Se l'attività segnalata ha un effetto sulla sicurezza, denota la gravità dell'effetto. | Evento |
| EventOriginalUid | Stringa | af6ae8fe-ff43-4a4c-b537-8635976a2b51 | ID record del dispositivo di report. | Evento |
| EventStartTime | Data/ora | Vedere "tipi di dati" | Data e ora in cui l'evento è stato dichiarato | Evento |
| TimeGenerated | Data/ora | Vedere "tipi di dati" | Data e ora in cui si è verificato l'evento, segnalato dall'origine dei report. | Campo personalizzato |
| EventTimeIngested | Data/ora | Vedere "tipi di dati" | Il momento in cui l'evento è stato inserito in Sentinel di Azure. Verranno aggiunti da Azure Sentinel. | Evento |
| EventUid | GUID (stringa) | 516a64e3-8360-4f1e-a67c-d96b3d52df54 | Identificatore univoco utilizzato da Sentinel per contrassegnare una riga. | Evento |
| NetworkApplicationProtocol | Stringa | HTTPS | Protocollo di livello applicazione utilizzato dalla connessione o dalla sessione. | Rete |
| DstBytes | INT | 32455 | Numero di byte inviati dalla destinazione all'origine per la connessione o la sessione. | Destination |
| SrcBytes | INT | 46536 | Numero di byte inviati dall'origine alla destinazione per la connessione o la sessione. | Source (Sorgente) |
| NetworkBytes | INT | 78991 | Numero di byte inviati in entrambe le direzioni. Se sono presenti sia BytesReceived che BytesSent, BytesTotal deve essere uguale alla somma. | Rete |
| NetworkDirection | Multivalore: in ingresso, in uscita (stringa) | In ingresso | Direzione della connessione o della sessione, all'interno o all'esterno dell'organizzazione. | Rete |
| DstGeoCity | Stringa | Burlington | Città associata all'indirizzo IP di destinazione | Destinazione<br>Area geografica |
| DstGeoCountry | Country (stringa) | USA | Il paese associato all'indirizzo IP di origine | Destinazione<br>Area geografica |
| DstDvcHostname | Nome dispositivo (stringa) |  victim_pc | Nome del dispositivo di destinazione | Destination<br>Dispositivo |
| DstDvcFqdn | Stringa | victim_pc. contoso. local | Nome di dominio completo dell'host in cui è stato creato il log | Destinazione<br>Dispositivo |
| DstDomainHostname | string | CONTOSO | Il dominio della destinazione, il dominio dell'host di destinazione (sito Web, nome di dominio e così via), ad esempio per le ricerche DNS o le ricerche NS | Destination |
| DstInterfaceName | string | Scheda di rete Microsoft Hyper-V | Interfaccia di rete utilizzata per la connessione o la sessione dal dispositivo di destinazione. | Destination |
| DstInterfaceGuid | string | 2BB33827-6BB6-48DB-8DE6-DB9E0B9F9C9B | GUID dell'interfaccia di rete usata per la richiesta di autenticazione  | Destination |
| DstIpAddr | Indirizzo IP | 2001: DB8:: FF00:42:8329 | Indirizzo IP della destinazione della connessione o della sessione, più comunemente indicato come IP di destinazione nel pacchetto di rete | Destinazione<br>IP |
| DstDvcIpAddr | Indirizzo IP | 75.22.12.2 | Indirizzo IP di destinazione di un dispositivo non associato direttamente al pacchetto di rete | Destinazione<br>Dispositivo<br>IP
| DstGeoLatitude | Latitudine (Double) | 44,475833 | Latitudine della coordinata geografica associata all'indirizzo IP di destinazione | Destinazione<br>Area geografica |
| DstMacAddr | Stringa | 06:10:9F: EB: 8F: 14 | Indirizzo MAC dell'interfaccia di rete a cui è stata interrotta la connessione o la sessione, che in genere si fa riferimento al MAC di destinazione nel pacchetto di rete | Destinazione<br>MAC |
| DstDvcMacAddr | Stringa | 06:10:9F: EB: 8F: 14 | Indirizzo MAC di destinazione di un dispositivo non associato direttamente al pacchetto di rete. | Destinazione<br>Dispositivo<br>MAC |
| DstDvcDomain | Stringa | CONTOSO | Dominio del dispositivo di destinazione. | Destinazione<br>Dispositivo |
| DstPortNumber | Integer | 443 | Porta IP di destinazione. | Destinazione<br>Porta |
| DstGeoRegion | Region (stringa) | Vermont | Area all'interno di un paese associato all'indirizzo IP di destinazione | Destinazione<br>Area geografica |
| DstResourceId | ID dispositivo (stringa) |  /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /victim | ID risorsa del dispositivo di destinazione. | Destination |
| DstNatIpAddr | Indirizzo IP | 2::1 | Se segnalato da un dispositivo NAT intermediario, ad esempio un firewall, l'indirizzo IP usato dal dispositivo NAT per la comunicazione con l'origine. | NAT di destinazione,<br>IP |
| DstNatPortNumber | INT | 443 | Se segnalato da un dispositivo NAT intermediario, ad esempio un firewall, la porta usata dal dispositivo NAT per la comunicazione con l'origine. | NAT di destinazione,<br>Porta |
| DstUserSid | SID utente |  S-12-1445 | ID utente dell'identità associata alla destinazione della sessione. In genere, l'identità utilizzata per autenticare un server. Per informazioni dettagliate, vedere "tipi di dati". | Destinazione<br>Utente |
| DstUserAadId | Stringa (Guid) | ae92b0b4-cfba-4b42-85a0-fbd862f4df54 | ID dell'oggetto account Azure AD dell'utente nell'entità finale di destinazione della sessione | Destinazione<br>Utente |
| DstUserName | Nome utente (stringa) | LucaD | Nome utente dell'identità associata alla destinazione della sessione.  | Destinazione<br>Utente |
| DstUserUpn | string | johnd@anon.com | UPN dell'identità associata alla destinazione della sessione. | Destinazione<br>Utente |
| DstUserDomain | string | GRUPPO DI LAVORO | Nome del dominio o del computer dell'account nella destinazione della sessione | Destinazione<br>Utente |
| DstZone | Stringa | Rete perimetrale | Zona di rete della destinazione, in base a quanto definito dal dispositivo per la creazione di report. | Destination |
| DstGeoLongitude | Longitudine (Double) | -73,211944 | Longitudine della coordinata geografica associata all'indirizzo IP di destinazione | Destinazione<br>Area geografica |
| DvcAction | Multivalore: Allow, Deny, Drop (String) | Consenti | Se segnalato da un dispositivo intermediario, ad esempio un firewall, l'azione eseguita dal dispositivo. | Dispositivo |
| DvcInboundInterface | Stringa | eth0 | Se segnalato da un dispositivo intermediario, ad esempio un firewall, l'interfaccia di rete usata da tale dispositivo per la connessione al dispositivo di origine. | Dispositivo |
| DvcOutboundInterface | Stringa  | Scheda Ethernet Ethernet 4 | Se segnalato da un dispositivo intermediario, ad esempio un firewall, l'interfaccia di rete usata da tale dispositivo per la connessione al dispositivo di destinazione. | Dispositivo |
| NetworkDuration | Integer | 1500 | Quantità di tempo, in millisecondi, per il completamento della sessione o della connessione di rete | Rete |
| NetworkIcmpCode | Integer | 34 | Per un messaggio ICMP, tipo di messaggio ICMP valore numerico (RFC 2780 o RFC 4443). | Rete |
| NetworkIcmpType | Stringa | Destinazione non raggiungibile | Per un messaggio ICMP, rappresentazione testuale del tipo di messaggio ICMP (RFC 2780 o RFC 4443). | Rete |
| DstPackets | INT  | 446 | Numero di pacchetti inviati dalla destinazione all'origine per la connessione o la sessione. Il significato di un pacchetto viene definito dal dispositivo per la creazione di report. | Destination |
| SrcPackets | INT  | 6478 | Numero di pacchetti inviati dall'origine alla destinazione per la connessione o la sessione. Il significato di un pacchetto viene definito dal dispositivo per la creazione di report. | Source (Sorgente) |
| NetworkPackets | INT  | 0 | Numero di pacchetti inviati in entrambe le direzioni. Se sono presenti sia PacketsReceived che PacketsSent, BytesTotal deve essere uguale alla somma. | Rete |
| HttpRequestTime | Integer | 700 | Tempo impiegato per inviare la richiesta al server, se applicabile. | Http |
| HttpResponseTime | Integer | 800 | Quantità di tempo impiegato per ricevere una risposta nel server, se applicabile. | Http |
| NetworkRuleName | Stringa | AnyAnyDrop | Nome o ID della regola con cui è stato deciso DeviceAction | Rete |
| NetworkRuleNumber | INT |  23 | Numero regola corrispondente  | Rete |
| NetworkSessionId | string | 172_12_53_32_4322__123_64_207_1_80 | Identificatore di sessione segnalato dal dispositivo di creazione report. Ad esempio, l'identificatore di sessione L7 per applicazioni specifiche dopo l'autenticazione | Rete |
| SrcGeoCity | Stringa | Burlington | Città associata all'indirizzo IP di origine | Origine<br>Area geografica |
| SrcGeoCountry | Country (stringa) | USA | Il paese associato all'indirizzo IP di origine | Origine<br>Area geografica |
| SrcDvcHostname | Nome dispositivo (stringa) |  cattivo | Nome del dispositivo di origine | Origine<br>Dispositivo |
| SrcDvcFqdn | string | Villain.malicious.com | Nome di dominio completo dell'host in cui è stato creato il log | Origine<br>Dispositivo |
| SrcDvcDomain | string | EVILORG | Dominio del dispositivo da cui è stata avviata la sessione | Origine<br>Dispositivo |
| SrcDvcOs | Stringa | iOS | Sistema operativo del dispositivo di origine | Origine<br>Dispositivo |
| SrcDvcModelName | Stringa | Nota Samsung Galaxy | Nome del modello del dispositivo di origine | Origine<br>Dispositivo |
| SrcDvcModelNumber | Stringa | 10,0 | Il numero di modello del dispositivo di origine | Origine<br>Dispositivo |
| SrcDvcType | Stringa | Dispositivi mobili | Tipo del dispositivo di origine | Origine<br> Dispositivo |
| SrcIntefaceName | Stringa | eth01 | Interfaccia di rete usata per la connessione o la sessione dal dispositivo di origine. | Source (Sorgente) |
| SrcInterfaceGuid | Stringa | 46ad544b-eaf0-47ef-827c-266030f545a6 | GUID dell'interfaccia di rete utilizzata | Source (Sorgente) |
| SrcIpAddr | Indirizzo IP | 77.138.103.108 | Indirizzo IP da cui ha avuto origine la connessione o la sessione. | Origine<br>IP |
| SrcDvcIpAddr | Indirizzo IP | 77.138.103.108 | Indirizzo IP di origine di un dispositivo non associato direttamente al pacchetto di rete (raccolto da un provider o calcolato in modo esplicito). | Origine<br>Dispositivo<br>IP |
| SrcGeoLatitude | Latitudine (Double) | 44,475833 | Latitudine della coordinata geografica associata all'indirizzo IP di origine | Origine<br>Area geografica |
| SrcGeoLongitude | Longitudine (Double) | -73,211944 | Longitudine della coordinata geografica associata all'indirizzo IP di origine | Origine<br>Area geografica |
| SrcMacAddr | Stringa | 06:10:9F: EB: 8F: 14 | Indirizzo MAC dell'interfaccia di rete da cui ha avuto origine la sessione di connessione od. | Origine<br>Mac |
| SrcDvcMacAddr | Stringa | 06:10:9F: EB: 8F: 14 | Indirizzo MAC di origine di un dispositivo che non è direttamente associato al pacchetto di rete. | Origine<br>Dispositivo<br>Mac |
| SrcPortNumber | Integer | 2335 | Porta IP da cui ha avuto origine la connessione. Potrebbe non essere pertinente per una sessione che include più connessioni. | Origine<br>Porta |
| SrcGeoRegion | Region (stringa) | Vermont | Area all'interno di un paese associato all'indirizzo IP di origine | Origine<br>Area geografica |
| SrcResourceId | Stringa | /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /syslogserver1 | ID risorsa del dispositivo che genera il messaggio. | Source (Sorgente) |
| SrcNatIpAddr | Indirizzo IP | 4.3.2.1 | Se segnalato da un dispositivo NAT intermediario, ad esempio un firewall, l'indirizzo IP usato dal dispositivo NAT per la comunicazione con la destinazione. | NAT di origine,<br>IP |
| SrcNatPortNumber | Integer | 345 | Se segnalato da un dispositivo NAT intermediario, ad esempio un firewall, la porta usata dal dispositivo NAT per la comunicazione con la destinazione. | NAT di origine,<br>Porta |
| SrcUserSid | ID utente (stringa) | S-15-1445 | ID utente dell'identità associata all'origine delle sessioni. In genere, l'utente esegue un'azione nel client. Per informazioni dettagliate, vedere "tipi di dati". | Origine<br>Utente |
| SrcUserAadId | Stringa (Guid) | 16c8752c-7dd2-4cad-9e03-fb5d1cee5477 | ID dell'oggetto account Azure AD dell'utente all'estremità di origine della sessione | Origine<br>Utente |
| SrcUserName | Nome utente (stringa) | Bob | Nome utente dell'identità associata all'origine delle sessioni. In genere, l'utente esegue un'azione nel client. Per informazioni dettagliate, vedere "tipi di dati". | Source (Sorgente)<br>Utente |
| SrcUserUpn | string | bob@alice.com | UPN dell'account che avvia la sessione | Origine<br>Utente |
| SrcUserDomain | string | DESKTOP | Il dominio per l'account che avvia la sessione | Origine<br>Utente |
| SrcZone | Stringa | Tocco | Zona di rete dell'origine, in base a quanto definito dal dispositivo per la creazione di report. | Source (Sorgente) |
| NetworkProtocol | Stringa | TCP | Protocollo IP utilizzato dalla connessione o dalla sessione. In genere TCP, UDP o ICMP | Rete |
| CloudAppName | Stringa | Facebook | Nome dell'applicazione di destinazione per un'applicazione HTTP come identificato da un proxy. | Cloud |
| CloudAppId | Stringa | 124 | ID dell'applicazione di destinazione per un'applicazione HTTP come identificato da un proxy. Questo valore è in genere specifico del proxy utilizzato. | Cloud |
| CloudAppOperation | Stringa | DeleteFile | Operazione eseguita dall'utente nel contesto dell'applicazione di destinazione per un'applicazione HTTP identificata da un proxy. Questo valore è in genere specifico del proxy utilizzato. | Cloud |
| CloudAppRiskLevel | Stringa | 3 | Livello di rischio associato a un'applicazione HTTP identificato da un proxy. Questo valore è in genere specifico del proxy utilizzato. | Cloud |
| FileName | string | ImNotMalicious.exe | Nome del file trasmesso sulle connessioni di rete per protocolli quali FTP e HTTP che forniscono le informazioni sul nome del file. | File |
| FilePath | Stringa | C:\Malicious\ImNotMalicious.exe | Percorso completo, incluso il nome file, del file | File |
| FileHashMd5 | Stringa | 51BC68715FC7C109DCEA406B42D9D78F | Valore hash MD5 del file trasmesso sulle connessioni di rete per i protocolli. | File |
| FileHashSha1 | Stringa | 491AE3... C299821476F4 | Valore hash SHA1 del file trasmesso sulle connessioni di rete per i protocolli. | File |
| FileHashSha256 | Stringa | 9B8F8EDB... C129976F03 | Valore hash SHA256 del file trasmesso sulle connessioni di rete per i protocolli. | File |
| FileHashSha512 | Stringa | 5E127D... F69F73F01F361 | Valore hash SHA512 del file trasmesso sulle connessioni di rete per i protocolli. | File |
| FileExtension |  Stringa | exe | Tipo di file trasmesso sulle connessioni di rete per protocolli quali FTP e HTTP. | File
| FileMimeType | Stringa | applicazione/MSWord | Tipo MIME del file trasmesso sulle connessioni di rete per protocolli quali FTP e HTTP | File |
| FileSize | Integer | 23500 | Dimensioni del file, in byte, del file trasmesso sulle connessioni di rete per i protocolli. | File |
| HttpVersion | Stringa | 2.0 | Versione della richiesta HTTP per le connessioni di rete HTTP/HTTPS. | Http |
| HttpRequestMethod | Stringa | GET | Metodo HTTP per le sessioni di rete HTTP/HTTPS. | Http |
| HttpStatusCode | Stringa | 404 | Codice di stato HTTP per le sessioni di rete HTTP/HTTPS. | Http |
| HttpContentType | Stringa | multipart/form-data; limite = qualcosa | Intestazione del tipo di contenuto della risposta HTTP per le sessioni di rete HTTP/HTTPS. | Http |
| HttpReferrerOriginal | Stringa | https://developer.mozilla.org/en-US/docs/Web/JavaScript | Intestazione del referrer HTTP per le sessioni di rete HTTP/HTTPS. | Http |
| HttpUserAgentOriginal | Stringa | Mozilla/5.0 (Windows NT 10,0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/83.0.4103.97 Safari/537.36 | Intestazione agente utente HTTP per le sessioni di rete HTTP/HTTPS. | Http |
| HttpRequestXff | Stringa | 120.12.41.1 | Intestazione HTTP X-Inoltred-for per le sessioni di rete HTTP/HTTPS. | Http |
| UrlCategory | Stringa | Motori di ricerca | Il raggruppamento definito di un URL (o potrebbe essere basato solo sul dominio nell'URL) correlato a ciò che è (ad esempio, per adulti, notizie, pubblicità, domini parcheggiati e così via) | url |
| UrlOriginal | Stringa | https://contoso.com/fo/?k=v&q = u # f | URL della richiesta HTTP per le sessioni di rete HTTP/HTTPS. | URL |
| UrlHostname | Stringa | contoso.com | Parte del dominio di un URL di richiesta HTTP per le sessioni di rete HTTP/HTTPS. | URL |
| ThreatCategory | Stringa | Trojan | La categoria di una minaccia identificata da un sistema di sicurezza, ad esempio il gateway di sicurezza Web di un IP, è associata a questa sessione di rete. | Minaccia |
| ThreatId | Stringa | TR. 124 | ID di una minaccia identificata da un sistema di sicurezza, ad esempio un gateway di sicurezza Web di un IP, associato a questa sessione di rete. | Minaccia |
| Threatname | Stringa | File di test di EICAR | Nome della minaccia o del malware identificato | Minaccia |
| AdditionalFields | Dynamic (contenitore JSON) | {<br>Property1: "Val1",<br>Property2: "val2"<br>} | Quando nessuna colonna corrispondente nello schema corrisponde, i campi aggiuntivi possono essere archiviati in un contenitore JSON.<br>Per l'analisi in fase di query, è consigliabile non usare questo metodo perché la compressione dei dati in un file JSON comporta un peggioramento delle prestazioni di esecuzione delle query. È invece consigliabile innalzare di livello colonne aggiuntive.<br>Per gli scenari di analisi del tempo di inserimento futuri, i dati aggiuntivi verranno naturalmente raccolti in questa colonna del contenitore JSON. | Campo personalizzato |
| 

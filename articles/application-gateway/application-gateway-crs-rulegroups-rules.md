---
title: Regole e gruppi di regole CRS del Web application firewall di Gateway applicazione di Azure | Microsoft Docs
description: Questa pagina offre informazioni sulle regole e i gruppi di regole CRS del Web application firewall.
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 03/28/2017
ms.author: davidmu
ms.translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 9f4445b7ceb5b39c7907b1ca4c7bf0ad52ba8212
ms.contentlocale: it-it
ms.lasthandoff: 03/30/2017

---

# <a name="list-of-web-application-firewall-crs-rule-groups-and-rules-offered"></a>Elenco delle regole e dei gruppi di regole CRS del Web application firewall disponibili

Il Web application firewall (WAF) di Gateway applicazione protegge le applicazioni Web da vulnerabilità ed exploit comuni. A questo scopo vengono usate regole definite in base a OWASP Core Rule Set 2.2.9 o 3.0. Queste regole possono essere disabilitate singolarmente. Questo articolo contiene le regole e i set di regole attualmente offerti.

Nelle tabelle seguenti sono riportati le regole e i gruppi di regole disponibili quando si usa Gateway applicazione con il Web application firewall.  Ogni tabella rappresenta le regole incluse in un gruppo di regole per una specifica versione di CRS.

##<a name="owasp30"></a> OWASP_3.0

### <a name="crs910"></a>  <p x-ms-format-detection="none">REQUEST-910-IP-REPUTATION</p>

|ID regola|Descrizione|
|---|---|
|910011|Regola 910011|
|910012|Regola 910012|
|910000|Richiesta da client non autorizzato noto (in base a precedenti violazioni del traffico)|
|910100|IP client da paese ad ALTO rischio|
|910120|Regola 910120|
|910130|Regola 910130|
|910150|Corrispondenza in blacklist HTTP per IP motore di ricerca|
|910160|Corrispondenza in blacklist HTTP per IP spammer|
|910170|Corrispondenza in blacklist HTTP per IP sospetto|
|910180|Corrispondenza in blacklist HTTP per IP responsabile di harvesting|
|910013|Regola 910013|
|910014|Regola 910014|
|910015|Regola 910015|
|910016|Regola 910016|
|910017|Regola 910017|
|910018|Regola 910018|

### <a name="crs911"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|ID regola|Descrizione|
|---|---|
|911011|Regola 911011|
|911012|Regola 911012|
|911100|Metodo non consentito da criteri|
|911013|Regola 911013|
|911014|Regola 911014|
|911015|Regola 911015|
|911016|Regola 911016|
|911017|Regola 911017|
|911018|Regola 911018|

### <a name="crs912"></a> <p x-ms-format-detection="none">REQUEST-912-DOS-PROTECTION</p>

|ID regola|Descrizione|
|---|---|
|912100|Regola 912100|
|912012|Regola 912012|
|912120|Attacco Denial of Service (DoS) identificato da %@{tx.real_ip} (%@{tx.dos_block_counter} riscontri dall'ultimo avviso)|
|912130|Regola 912130|
|912140|Regola 912140|
|912150|Regola 912150|
|912160|Regola 912160|
|912170|Potenziale attacco Denial of Service (DoS) da %@{tx.real_ip} - n. di burst richieste: %@{ip.dos_burst_counter}|
|912013|Regola 912013|
|912014|Regola 912014|
|912019|Regola 912019|
|912171|Potenziale attacco Denial of Service (DoS) da %@{tx.real_ip} - n. di burst richieste: %@{ip.dos_burst_counter}|
|912015|Regola 912015|
|912016|Regola 912016|
|912017|Regola 912017|
|912018|Regola 912018|

### <a name="crs913"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|ID regola|Descrizione|
|---|---|
|913011|Regola 913011|
|913012|Regola 913012|
|913100|Trovato agente utente associato ad analisi della sicurezza|
|913110|Trovata intestazione della richiesta associata ad analisi della sicurezza|
|913120|Trovato argomento/nome file della richiesta associato ad analisi della sicurezza|
|913013|Regola 913013|
|913014|Regola 913014|
|913101|Trovato agente utente associato a client HTTP generico/di scripting|
|913102|Trovato agente utente associato a bot/agente di ricerca Web|
|913015|Regola 913015|
|913016|Regola 913016|
|913017|Regola 913017|
|913018|Regola 913018|

### <a name="crs920"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|ID regola|Descrizione|
|---|---|
|920011|Regola 920011|
|920012|Regola 920012|
|920100|Riga della richiesta HTTP non valida|
|920130|Impossibile analizzare corpo della richiesta|
|920140|La convalida di tipo strict del corpo della richiesta multiparte non è riuscita:     PE %@{REQBODY_PROCESSOR_ERROR}     BQ %@{MULTIPART_BOUNDARY_QUOTED}     BW %@{MULTIPART_BOUNDARY_WHITESPACE}     DB %@{MULTIPART_DATA_BEFORE}     DA %@{MULTIPART_DATA_AFTER}     HF %@{MULTIPART_HEADER_FOLDING}     LF %@{MULTIPART_LF_LINE}     SM %@{MULTIPART_SEMICOLON_MISSING}     IQ %@{MULTIPART_INVALID_QUOTING}     IH %@{MULTIPART_INVALID_HEADER_FOLDING}     FLE %@{MULTIPART_FILE_LIMIT_EXCEEDED}|
|920160|Intestazione HTTP Content-Length non numerica|
|920170|Richiesta GET o HEAD con contenuto del corpo|
|920180|Richiesta POST senza intestazione Content-Length|
|920190|Intervallo: valore ultimo byte non valido|
|920210|Trovati dati intestazione di connessione multipli/in conflitto|
|920220|Tentativo di attacco con uso improprio codifica URL|
|920240|Tentativo di attacco con uso improprio codifica URL|
|920250|Tentativo di attacco con uso improprio codifica UTF8|
|920260|Tentativo di attacco con uso improprio metà larghezza/larghezza intera Unicode|
|920270|Carattere non valido nella richiesta (carattere null)|
|920280|Richiesta senza intestazione host|
|920290|Intestazione host vuota|
|920310|Richiesta con intestazione Accept vuota|
|920311|Richiesta con intestazione Accept vuota|
|920330|Intestazione agente utente vuota|
|920340|Richiesta con contenuto ma senza intestazione Content-Type|
|920350|Intestazione host costituita da un indirizzo IP numerico|
|920380|Troppi argomenti nella richiesta|
|920360|Nome argomento troppo lungo|
|920370|Valore argomento troppo lungo|
|920390|Superate dimensioni totali argomenti|
|920400|Dimensione file caricato troppo grande|
|920410|Dimensione totale dei file caricati troppo grande|
|920420|Tipo di contenuto della richiesta non consentito da criteri|
|920430|Versione protocollo HTTP non consentita da criteri|
|920440|Estensione file URL limitata da criteri|
|920450|Intestazione HTTP limitata da criteri (%@{MATCHED_VAR})|
|920013|Regola 920013|
|920014|Regola 920014|
|920200|Intervallo: troppi campi (6 o più)|
|920201|Intervallo: troppi campi per richiesta PDF (35 o più)|
|920230|Rilevata codifica multipla URL|
|920300|Richiesta senza intestazione Accept|
|920271|Carattere non valido nella richiesta (caratteri non stampabili)|
|920320|Intestazione agente utente mancante|
|920015|Regola 920015|
|920016|Regola 920016|
|920272|Carattere non valido nella richiesta (non compreso nei caratteri stampabili sotto ASCII 127)|
|920017|Regola 920017|
|920018|Regola 920018|
|920202|Intervallo: troppi campi per richiesta PDF (6 o più)|
|920273|Carattere non valido nella richiesta (non compreso in set molto restrittivo)|
|920274|Carattere non valido nelle intestazioni della richiesta (non compreso in set con restrizioni elevate)|
|920460|Regola 920460|

### <a name="crs921"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|ID regola|Descrizione|
|---|---|
|921011|Regola 921011|
|921012|Regola 921012|
|921100|Attacco di tipo HTTP Request Smuggling|
|921110|Attacco di tipo HTTP Request Smuggling|
|921120|Attacco di tipo HTTP Response Splitting|
|921130|Attacco di tipo HTTP Response Splitting|
|921140|Attacco di tipo HTTP Header Injection tramite intestazioni|
|921150|Attacco di tipo HTTP Header Injection tramite payload (rilevato CR/LF)|
|921160|Attacco di tipo HTTP Header Injection tramite payload (rilevati CR/LF e nome intestazione)|
|921013|Regola 921013|
|921014|Regola 921014|
|921151|Attacco di tipo HTTP Header Injection tramite payload (rilevato CR/LF)|
|921015|Regola 921015|
|921016|Regola 921016|
|921170|Regola 921170|
|921180|Accesso non autorizzato a parametri HTTP (%@{TX.1})|
|921017|Regola 921017|
|921018|Regola 921018|

### <a name="crs930"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|ID regola|Descrizione|
|---|---|
|930011|Regola 930011|
|930012|Regola 930012|
|930100|Attacco di tipo Path Traversal (/../)|
|930110|Attacco di tipo Path Traversal (/../)|
|930120|Tentativo di accesso a file del sistema operativo|
|930130|Tentativo di accesso a file con restrizioni|
|930013|Regola 930013|
|930014|Regola 930014|
|930015|Regola 930015|
|930016|Regola 930016|
|930017|Regola 930017|
|930018|Regola 930018|

### <a name="crs931"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|ID regola|Descrizione|
|---|---|
|931011|Regola 931011|
|931012|Regola 931012|
|931100|Possibile attacco di tipo Remote File Inclusion (RFI): parametro URL con indirizzo IP|
|931110|Possibile attacco di tipo Remote File Inclusion (RFI): nome parametro vulnerabile a RFI comune usato con payload URL|
|931120|Possibile attacco di tipo Remote File Inclusion (RFI): payload URL usato con carattere punto interrogativo (?) finale|
|931013|Regola 931013|
|931014|Regola 931014|
|931130|Possibile attacco di tipo Remote File Inclusion (RFI): collegamento/riferimento fuori dominio|
|931015|Regola 931015|
|931016|Regola 931016|
|931017|Regola 931017|
|931018|Regola 931018|

### <a name="crs932"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|ID regola|Descrizione|
|---|---|
|932011|Regola 932011|
|932012|Regola 932012|
|932120|Esecuzione comandi in remoto: trovato comando di Windows PowerShell|
|932130|Esecuzione comandi in remoto: trovata espressione shell Unix|
|932140|Esecuzione comandi in remoto: trovato comando FOR/IF di Windows|
|932160|Esecuzione comandi in remoto: trovato codice shell Unix|
|932170|Esecuzione comandi in remoto: Shellshock (CVE-2014-6271)|
|932171|Esecuzione comandi in remoto: Shellshock (CVE-2014-6271)|
|932013|Regola 932013|
|932014|Regola 932014|
|932015|Regola 932015|
|932016|Regola 932016|
|932017|Regola 932017|
|932018|Regola 932018|

### <a name="crs933"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|ID regola|Descrizione|
|---|---|
|933011|Regola 933011|
|933012|Regola 933012|
|933100|Attacco PHP injection: trovato tag di apertura/chiusura|
|933110|Attacco PHP injection: trovato caricamento file script PHP|
|933120|Attacco PHP injection: trovata direttiva di configurazione|
|933130|Attacco PHP injection: trovate variabili|
|933150|Attacco PHP injection: trovato nome funzione PHP ad alto rischio|
|933160|Attacco PHP injection: trovata chiamata di funzione PHP ad alto rischio|
|933180|Attacco PHP injection: trovata chiamata di funzione variabile|
|933013|Regola 933013|
|933014|Regola 933014|
|933151|Attacco PHP injection: trovato nome funzione PHP a medio rischio|
|933015|Regola 933015|
|933016|Regola 933016|
|933131|Attacco PHP injection: trovate variabili|
|933161|Attacco PHP injection: trovata chiamata di funzione PHP con valore basso|
|933111|Attacco PHP injection: trovato caricamento file script PHP|
|933017|Regola 933017|
|933018|Regola 933018|

### <a name="crs941"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|ID regola|Descrizione|
|---|---|
|941011|Regola 941011|
|941012|Regola 941012|
|941100|Rilevato attacco XSS tramite libinjection|
|941110|Filtro XSS, categoria 1: vettore tag script|
|941130|Filtro XSS, categoria 3: vettore attributi|
|941140|Filtro XSS, categoria 4: vettore URI Javascript|
|941150|Filtro XSS, categoria 5: attributi HTML non consentiti|
|941180|Parole chiave in blacklist convalida nodi|
|941190|Filtri XSS IE: rilevato attacco|
|941200|Filtri XSS IE: rilevato attacco|
|941210|Filtri XSS IE: rilevato attacco|
|941220|Filtri XSS IE: rilevato attacco|
|941230|Filtri XSS IE: rilevato attacco|
|941240|Filtri XSS IE: rilevato attacco|
|941260|Filtri XSS IE: rilevato attacco|
|941270|Filtri XSS IE: rilevato attacco|
|941280|Filtri XSS IE: rilevato attacco|
|941290|Filtri XSS IE: rilevato attacco|
|941300|Filtri XSS IE: rilevato attacco|
|941310|Filtro XSS per codifica US-ASCII in formato non valido: rilevato attacco|
|941350|XSS IE con codifica UTF-7: rilevato attacco|
|941013|Regola 941013|
|941014|Regola 941014|
|941320|Rilevato possibile attacco XSS: gestore tag HTML|
|941015|Regola 941015|
|941016|Regola 941016|
|941017|Regola 941017|
|941018|Regola 941018|

### <a name="crs942"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|ID regola|Descrizione|
|---|---|
|942011|Regola 942011|
|942012|Regola 942012|
|942100|Rilevato attacco SQL injection tramite libinjection|
|942140|Attacco SQL injection: rilevati nomi DB comuni|
|942160|Rilevamento test di blind SQL injection con sleep() o benchmark()|
|942170|Rilevamento tentativi di SQL injection con benchmark e sleep e query condizionali|
|942230|Rilevamento tentativi di SQL injection condizionale|
|942270|Ricerca di SQL injection di base. Stringa di attacco comune per MySQL, Oracle e altri|
|942290|Ricerca tentativi di SQL injection di base in MongoDB|
|942320|Rilevamento inserimenti di stored procedure/funzioni MySQL e PostgreSQL|
|942350|Rilevamento di inserimento di funzioni definite dall'utente MySQL e altri tentativi di manipolazione dati/struttura|
|942013|Regola 942013|
|942014|Regola 942014|
|942150|Attacco SQL injection|
|942410|Attacco SQL injection|
|942440|Rilevata sequenza commenti SQL|
|942450|Identificata codifica esadecimale SQL|
|942015|Regola 942015|
|942016|Regola 942016|
|942251|Rilevamento inserimenti HAVING|
|942460|Avviso di rilevamento anomalie metacaratteri: caratteri non alfanumerici ripetitivi|
|942017|Regola 942017|
|942018|Regola 942018|

### <a name="crs943"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|ID regola|Descrizione|
|---|---|
|943011|Regola 943011|
|943012|Regola 943012|
|943100|Possibile attacco di tipo Session Fixation: impostazione valori cookie in HTML|
|943110|Possibile attacco di tipo Session Fixation: nome parametro SessionID con referrer fuori dominio|
|943120|Possibile attacco di tipo Session Fixation: nome parametro SessionID senza referrer|
|943013|Regola 943013|
|943014|Regola 943014|
|943015|Regola 943015|
|943016|Regola 943016|
|943017|Regola 943017|
|943018|Regola 943018|

##<a name="owasp229"></a> OWASP_2.2.9

### <a name="crs20"></a> crs_20_protocol_violations

|ID regola|Descrizione|
|---|---|
|960911|Riga della richiesta HTTP non valida|
|981227|Errore Apache: URI non valido nella richiesta|
|960912|Impossibile analizzare corpo della richiesta|
|960914|La convalida di tipo strict del corpo della richiesta multiparte non è riuscita:     PE %@{REQBODY_PROCESSOR_ERROR}     BQ %@{MULTIPART_BOUNDARY_QUOTED}     BW %@{MULTIPART_BOUNDARY_WHITESPACE}     DB %@{MULTIPART_DATA_BEFORE}     DA %@{MULTIPART_DATA_AFTER}     HF %@{MULTIPART_HEADER_FOLDING}     LF %@{MULTIPART_LF_LINE}     SM %@{MULTIPART_SEMICOLON_MISSING}     IQ %@{MULTIPART_INVALID_QUOTING}     IH %@{MULTIPART_INVALID_HEADER_FOLDING}     FLE %@{MULTIPART_FILE_LIMIT_EXCEEDED}|
|960915|Il parser multiparte ha rilevato un possibile limite senza corrispondenza|
|960016|Intestazione HTTP Content-Length non numerica|
|960011|Richiesta GET o HEAD con contenuto del corpo|
|960012|Richiesta POST senza intestazione Content-Length|
|960902|Uso non valido codifica identità|
|960022|Intestazione Expect non consentita per HTTP 1.0|
|960020|L'intestazione Pragma richiede l'intestazione Cache-Control per le richieste HTTP/1.1|
|958291|Intervallo: campo esistente che inizia con 0|
|958230|Intervallo: valore ultimo byte non valido|
|958295|Trovati dati intestazione di connessione multipli/in conflitto|
|950107|Tentativo di attacco con uso improprio codifica URL|
|950109|Rilevata codifica multipla URL|
|950108|Tentativo di attacco con uso improprio codifica URL|
|950801|Tentativo di attacco con uso improprio codifica UTF8|
|950116|Tentativo di attacco con uso improprio metà larghezza/larghezza intera Unicode|
|960901|Carattere non valido nella richiesta|
|960018|Carattere non valido nella richiesta|

### <a name="crs21"></a> crs_21_protocol_anomalies

|ID regola|Descrizione|
|---|---|
|960008|Richiesta senza intestazione host|
|960007|Intestazione host vuota|
|960015|Richiesta senza intestazione Accept|
|960021|Richiesta con intestazione Accept vuota|
|960009|Richiesta senza intestazione agente utente|
|960006|Intestazione agente utente vuota|
|960904|Richiesta con contenuto ma senza intestazione Content-Type|
|960017|Intestazione host costituita da un indirizzo IP numerico|

### <a name="crs23"></a> crs_23_request_limits

|ID regola|Descrizione|
|---|---|
|960209|Nome argomento troppo lungo|
|960208|Valore argomento troppo lungo|
|960335|Troppi argomenti nella richiesta|
|960341|Superate dimensioni totali argomenti|
|960342|Dimensione file caricato troppo grande|
|960343|Dimensione totale dei file caricati troppo grande|

### <a name="crs30"></a> crs_30_http_policy

|ID regola|Descrizione|
|---|---|
|960032|Metodo non consentito da criteri|
|960010|Tipo di contenuto della richiesta non consentito da criteri|
|960034|Versione protocollo HTTP non consentita da criteri|
|960035|Estensione file URL limitata da criteri|
|960038|Intestazione HTTP limitata da criteri|

### <a name="crs35"></a> crs_35_bad_robots

|ID regola|Descrizione|
|---|---|
|990002|La richiesta indica che è stata eseguita l'analisi della sicurezza sul sito|
|990901|La richiesta indica che è stata eseguita l'analisi della sicurezza sul sito|
|990902|La richiesta indica che è stata eseguita l'analisi della sicurezza sul sito|
|990012|Agente di ricerca siti Web non autorizzato|

### <a name="crs40"></a> crs_40_generic_attacks

|ID regola|Descrizione|
|---|---|
|960024|Avviso di rilevamento anomalie metacaratteri: caratteri non alfanumerici ripetitivi|
|950008|Inserimento di tag ColdFusion non documentati|
|950010|Attacco LDAP injection|
|950011|Attacco SSI injection|
|950018|Rilevato URL Universal PDF XSS|
|950019|Attacco e-mail injection|
|950012|Attacco di tipo HTTP Request Smuggling|
|950910|Attacco di tipo HTTP Response Splitting|
|950911|Attacco di tipo HTTP Response Splitting|
|950117|Attacco di tipo Remote File Inclusion|
|950118|Attacco di tipo Remote File Inclusion|
|950119|Attacco di tipo Remote File Inclusion|
|950120|Possibile attacco di tipo Remote File Inclusion (RFI): collegamento/riferimento fuori dominio|
|981133|Regola 981133|
|981134|Regola 981134|
|950009|Attacco di tipo Session Fixation|
|950003|Session Fixation|
|950000|Session Fixation|
|950005|Tentativo di accesso a file remoto|
|950002|Accesso a comandi di sistema|
|950006|Inserimento di comandi di sistema|
|959151|Attacco PHP injection|
|958976|Attacco PHP injection|
|958977|Attacco PHP injection|

### <a name="crs41sql"></a> crs_41_sql_injection_attacks

|ID regola|Descrizione|
|---|---|
|981231|Rilevata sequenza commenti SQL|
|981260|Identificata codifica esadecimale SQL|
|981320|Attacco SQL injection: rilevati nomi DB comuni|
|981300|Regola 981300|
|981301|Regola 981301|
|981302|Regola 981302|
|981303|Regola 981303|
|981304|Regola 981304|
|981305|Regola 981305|
|981306|Regola 981306|
|981307|Regola 981307|
|981308|Regola 981308|
|981309|Regola 981309|
|981310|Regola 981310|
|981311|Regola 981311|
|981312|Regola 981312|
|981313|Regola 981313|
|981314|Regola 981314|
|981315|Regola 981315|
|981316|Regola 981316|
|981317|Avviso di rilevamento anomalie in istruzione SQL SELECT|
|950007|Attacco blind SQL injection|
|950001|Attacco SQL injection|
|950908|Attacco SQL injection|
|959073|Attacco SQL injection|
|981272|Rilevamento test di blind SQL injection con sleep() o benchmark()|
|981250|Rilevamento tentativi di SQL injection con benchmark e sleep e query condizionali|
|981241|Rilevamento tentativi di SQL injection condizionale|
|981276|Ricerca di SQL injection di base. Stringa di attacco comune per MySQL, Oracle e altri|
|981270|Ricerca tentativi di SQL injection di base in MongoDB|
|981253|Rilevamento inserimenti di stored procedure/funzioni MySQL e PostgreSQL|
|981251|Rilevamento di inserimento di funzioni definite dall'utente MySQL e altri tentativi di manipolazione dati/struttura|

### <a name="crs41xss"></a> crs_41_xss_attacks

|ID regola|Descrizione|
|---|---|
|973336|Filtro XSS, categoria 1: vettore tag script|
|973338|Filtro XSS, categoria 3: vettore URI Javascript|
|981136|Regola 981136|
|981018|Regola 981018|
|958016|Attacco di tipo cross-site scripting (XSS)|
|958414|Attacco di tipo cross-site scripting (XSS)|
|958032|Attacco di tipo cross-site scripting (XSS)|
|958026|Attacco di tipo cross-site scripting (XSS)|
|958027|Attacco di tipo cross-site scripting (XSS)|
|958054|Attacco di tipo cross-site scripting (XSS)|
|958418|Attacco di tipo cross-site scripting (XSS)|
|958034|Attacco di tipo cross-site scripting (XSS)|
|958019|Attacco di tipo cross-site scripting (XSS)|
|958013|Attacco di tipo cross-site scripting (XSS)|
|958408|Attacco di tipo cross-site scripting (XSS)|
|958012|Attacco di tipo cross-site scripting (XSS)|
|958423|Attacco di tipo cross-site scripting (XSS)|
|958002|Attacco di tipo cross-site scripting (XSS)|
|958017|Attacco di tipo cross-site scripting (XSS)|
|958007|Attacco di tipo cross-site scripting (XSS)|
|958047|Attacco di tipo cross-site scripting (XSS)|
|958410|Attacco di tipo cross-site scripting (XSS)|
|958415|Attacco di tipo cross-site scripting (XSS)|
|958022|Attacco di tipo cross-site scripting (XSS)|
|958405|Attacco di tipo cross-site scripting (XSS)|
|958419|Attacco di tipo cross-site scripting (XSS)|
|958028|Attacco di tipo cross-site scripting (XSS)|
|958057|Attacco di tipo cross-site scripting (XSS)|
|958031|Attacco di tipo cross-site scripting (XSS)|
|958006|Attacco di tipo cross-site scripting (XSS)|
|958033|Attacco di tipo cross-site scripting (XSS)|
|958038|Attacco di tipo cross-site scripting (XSS)|
|958409|Attacco di tipo cross-site scripting (XSS)|
|958001|Attacco di tipo cross-site scripting (XSS)|
|958005|Attacco di tipo cross-site scripting (XSS)|
|958404|Attacco di tipo cross-site scripting (XSS)|
|958023|Attacco di tipo cross-site scripting (XSS)|
|958010|Attacco di tipo cross-site scripting (XSS)|
|958411|Attacco di tipo cross-site scripting (XSS)|
|958422|Attacco di tipo cross-site scripting (XSS)|
|958036|Attacco di tipo cross-site scripting (XSS)|
|958000|Attacco di tipo cross-site scripting (XSS)|
|958018|Attacco di tipo cross-site scripting (XSS)|
|958406|Attacco di tipo cross-site scripting (XSS)|
|958040|Attacco di tipo cross-site scripting (XSS)|
|958052|Attacco di tipo cross-site scripting (XSS)|
|958037|Attacco di tipo cross-site scripting (XSS)|
|958049|Attacco di tipo cross-site scripting (XSS)|
|958030|Attacco di tipo cross-site scripting (XSS)|
|958041|Attacco di tipo cross-site scripting (XSS)|
|958416|Attacco di tipo cross-site scripting (XSS)|
|958024|Attacco di tipo cross-site scripting (XSS)|
|958059|Attacco di tipo cross-site scripting (XSS)|
|958417|Attacco di tipo cross-site scripting (XSS)|
|958020|Attacco di tipo cross-site scripting (XSS)|
|958045|Attacco di tipo cross-site scripting (XSS)|
|958004|Attacco di tipo cross-site scripting (XSS)|
|958421|Attacco di tipo cross-site scripting (XSS)|
|958009|Attacco di tipo cross-site scripting (XSS)|
|958025|Attacco di tipo cross-site scripting (XSS)|
|958413|Attacco di tipo cross-site scripting (XSS)|
|958051|Attacco di tipo cross-site scripting (XSS)|
|958420|Attacco di tipo cross-site scripting (XSS)|
|958407|Attacco di tipo cross-site scripting (XSS)|
|958056|Attacco di tipo cross-site scripting (XSS)|
|958011|Attacco di tipo cross-site scripting (XSS)|
|958412|Attacco di tipo cross-site scripting (XSS)|
|958008|Attacco di tipo cross-site scripting (XSS)|
|958046|Attacco di tipo cross-site scripting (XSS)|
|958039|Attacco di tipo cross-site scripting (XSS)|
|958003|Attacco di tipo cross-site scripting (XSS)|
|973300|Rilevato possibile attacco XSS: gestore tag HTML|
|973301|Rilevato attacco XSS|
|973302|Rilevato attacco XSS|
|973303|Rilevato attacco XSS|
|973304|Rilevato attacco XSS|
|973305|Rilevato attacco XSS|
|973306|Rilevato attacco XSS|
|973307|Rilevato attacco XSS|
|973308|Rilevato attacco XSS|
|973309|Rilevato attacco XSS|
|973311|Rilevato attacco XSS|
|973313|Rilevato attacco XSS|
|973314|Rilevato attacco XSS|
|973331|Filtri XSS IE: rilevato attacco|
|973315|Filtri XSS IE: rilevato attacco|
|973330|Filtri XSS IE: rilevato attacco|
|973327|Filtri XSS IE: rilevato attacco|
|973326|Filtri XSS IE: rilevato attacco|
|973346|Filtri XSS IE: rilevato attacco|
|973345|Filtri XSS IE: rilevato attacco|
|973324|Filtri XSS IE: rilevato attacco|
|973323|Filtri XSS IE: rilevato attacco|
|973348|Filtri XSS IE: rilevato attacco|
|973321|Filtri XSS IE: rilevato attacco|
|973320|Filtri XSS IE: rilevato attacco|
|973318|Filtri XSS IE: rilevato attacco|
|973317|Filtri XSS IE: rilevato attacco|
|973329|Filtri XSS IE: rilevato attacco|
|973328|Filtri XSS IE: rilevato attacco|

### <a name="crs42"></a> crs_42_tight_security

|ID regola|Descrizione|
|---|---|
|950103|Attacco di tipo Path Traversal|

### <a name="crs45"></a> crs_45_trojans

|ID regola|Descrizione|
|---|---|
|950110|Accesso backdoor|
|950921|Accesso backdoor|
|950922|Accesso backdoor|

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come disabilitare le regole WAF, vedere l'articolo su come [personalizzare le regole del Web application firewall](application-gateway-customize-waf-rules-portal.md)

[1]: ./media/application-gateway-integration-security-center/figure1.png

<properties 
	pageTitle="Informazioni sulle funzionalità delle edizioni di Servizi BizTalk | Azure" 
	description="Confrontare le funzionalità delle edizioni dei Servizi BizTalk: Free, Developer, Basic, Standard e Premium. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="mandia"/>




# Servizi BizTalk: Grafico edizioni

Servizi BizTalk di Azure è disponibile in quattro edizioni: Free, Developer, Basic, Standard e Premium:

**Free (anteprima)**: consente di creare e gestire connessioni ibride. Una connessione ibrida è un modo semplice per connettere un sito Web di Azure a un sistema locale, ad esempio SQL Server. 

**Developer**: include connessioni ibride, l'elaborazione dei messaggi EAI e EDI con un portale di gestione dei partner commerciali facile da usare, il supporto di schemi EDI comuni e l'elaborazione avanzata di EDI su X12 e AS2. È possibile creare scenari EAI comuni per connettere i servizi nel cloud con i protocolli HTTP/S, REST, FTP, WCF e SFTP per la lettura e la scrittura di messaggi.  È possibile usare la connettività a sistemi line-of-business locali con adattatori SAP, Oracle eBusiness, Oracle DB, Siebel e SQL Server pronti all'uso. Il prodotto presenta un ambiente a misura di sviluppatore, con strumenti di Visual Studio per sviluppare e distribuire con facilità le applicazioni. Limitata allo sviluppo e all'esecuzione di test senza contratto di servizio.

**Basic**: Include la maggior parte delle funzionalità dell'edizione Developer con miglioramenti a livello di connessioni ibride, bridge EAI, contratti EDI e connessioni con BizTalk Adapter Pack. Offre inoltre disponibilità elevata e un'opzione per la scalabilità con un contratto di servizio.

**Standard**: include tutte le funzionalità dell'edizione Basic con miglioramenti a livello di connessioni ibride, bridge EAI, contratti EDI e connessioni con BizTalk Adapter Pack. Offre inoltre disponibilità elevata e un'opzione per la scalabilità con un contratto di servizio.


**Premium**: include tutte le funzionalità dell'edizione Standard con miglioramenti a livello di connessioni ibride, bridge EAI, contratti EDI e connessioni con BizTalk Adapter Pack. Offre inoltre archiviazione, disponibilità elevata e un'opzione per la scalabilità con un contratto di servizio.


Nella tabella seguente sono elencate le differenze tra le varie edizioni:

<table border="1">
<tr bgcolor="FAF9F9">
        <th></th>
        <th>Free (anteprima)</th>
        <th>Developer</th>
        <th>Basic</th>
        <th>Standard</th>
        <th>Premium</th>
</tr>
<tr>
<td><strong>Prezzo iniziale</strong></td>
<td>Vedere <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Dettagli prezzi servizi BizTalk di Azure</a>.</td>
<td>Vedere <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Dettagli prezzi servizi BizTalk di Azure</a>.</td>
<td>Vedere <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Dettagli prezzi servizi BizTalk di Azure</a>.</td>
<td>Vedere <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Dettagli prezzi servizi BizTalk di Azure</a>.</td>
<td>Vedere <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Dettagli prezzi servizi BizTalk di Azure</a>.</td>
</tr>
<tr>
<td><strong>Configurazione minima predefinita</strong></td>
<td>1 unità Free</td>
<td>1 unità Developer</td>
<td>1 unità Basic</td>
<td>1 unità Standard</td>
<td>1 unità Premium</td>
</tr>
<tr>
<td><strong>Scala</strong></td>
<td>Nessuna scalabilità</td>
<td>Nessuna scalabilità</td>
<td>Sì, con incrementi di 1 unità Basic</td>
<td>Sì, in incrementi di 1 unità Standard</td>
<td>Sì, in incrementi di 1 unità Premium</td>
</tr>
<tr>
<td><strong>Scalabilità orizzontale massima consentita</strong></td>
<td>Nessuna scalabilità</td>
<td>Nessuna scalabilità</td>
<td>Fino a 8 unità</td>
<td>Fino a 8 unità</td>
<td>Fino a 8 unità</td>
</tr>
<tr>
<td><strong>Bridge EAI per unità</strong></td>
<td>Non incluso</td>
<td>25</td>
<td>25</td>
<td>125</td>
<td>500</td>
</tr>
<tr>
<td><strong>EDI, AS2</strong>
<br/><br/>
Include contratti TPM</td>
<td>Non incluso</td>
<td>Incluso. 10 contratti per unità.</td>
<td>Incluso. 50 contratti per unità.</td>
<td>Incluso. 250 contratti per unità.</td>
<td>Incluso. 1000 contratti per unità.</td>
</tr>
<tr>
<td><strong>Connessioni ibride per unità</strong></td>
<td>5</td>
<td>5</td>
<td>10</td>
<td>50</td>
<td>100</td>
</tr>
<tr>
<td><strong>Trasferimento di dati di connessioni ibride (GB) per unità</strong></td>
<td>5</td>
<td>5</td>
<td>50</td>
<td>250</td>
<td>500</td>
</tr>
<tr>
<td><strong>Connessioni del servizio Adapter BizTalk a sistemi line-of-business locali</strong></td>
<td>Non incluso</td>
<td>1 connessione</td>
<td>2 connessioni</td>
<td>5 connessioni</td>
<td>25 connessioni</td>
</tr>
<tr>
<td align="left"><strong>Protocolli/sistemi supportati:</strong>
<ul>
<li>HTTP</li>
<li>HTTPS</li>
<li>FTP</li>
<li>SFTP</li>
<li>WCF</li>
<li>Bus di servizio</li>
<li>BLOB Azure</li>
<li>API REST</li>
</ul>
</td>
<td>Non incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
</tr>
<tr>
<td><strong>Disponibilità elevata</strong>
<br/><br/>
Per il contratto di servizi, vedere <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Dettagli prezzi servizi BizTalk</a>.
</td>
<td>Non incluso</td>
<td>Non incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
</tr>
<tr>
<td><strong>Backup e ripristino</strong></td>
<td>Non incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
</tr>
<tr>
<td><strong>Rilevamento</strong></td>
<td>Non incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
</tr>
<tr>
<td><strong>Archiviazione</strong><br/><br/>
Include ricevuta di non ripudio (NRR) e download dei messaggi rilevati</td>
<td>Non incluso</td>
<td>Incluso</td>
<td>Non incluso</td>
<td>Non incluso</td>
<td>Incluso</td>
</tr>
<tr>
<td><strong>Uso di codice personalizzato</strong></td>
<td>Non incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
</tr>
<tr>
<td><strong>Uso di trasformazioni, inclusi file XSLT personalizzati</strong></td>
<td>Non incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
</tr>
</table>

**Nota**
<br/>Per garantire la resilienza contro errori hardware, la disponibilità elevata richiede la presenza di macchine virtuali in un'unica unità di BizTalk.

## Domande frequenti
#### Che cos'è un'unità di BizTalk?
Un'unità è il livello atomico di una distribuzione di Servizi BizTalk di Azure. Ogni edizione include un'unità con capacità di calcolo e memoria diverse. Un'unità Basic, ad esempio, ha maggiori funzionalità di calcolo di un'unità Developer, una Standard ha maggiori funzionalità di calcolo di una Basic e così via. Quando si parla di scalabilità di Servizi BizTalk, la si intende in termini di unità.

#### Qual è la differenza tra Servizi BizTalk e una macchina virtuale BizTalk di Azure?
Servizi BizTalk offre una vera architettura di piattaforma distribuita come servizio (PaaS) per la creazione di soluzioni di integrazione nel cloud. Con il modello PaaS, l'utente si concentra completamente sulla logica dell'applicazione e lascia tutta la gestione dell'infrastruttura a Microsoft:

- Non è necessario gestire o installare patch sulle macchine virtuali
- Microsoft garantisce la disponibilità
- La scalabilità è controllata su richiesta semplicemente richiedendo una maggiore o minore capacità tramite il portale di gestione di Azure

BizTalk Server su Macchine virtuali di Azure offre un'architettura di infrastruttura distribuita come servizio (IaaS). L'utente crea le macchine virtuali e le configura esattamente come l'ambiente locale, semplificando l'esecuzione delle applicazioni esistenti nel cloud senza apportare modifiche al codice. Con IaaS, l'utente è comunque responsabile della configurazione delle macchine virtuali, della loro gestione (ad esempio l'installazione di software e patch per il sistema operativo) e della  progettazione dell'applicazione per la disponibilità elevata. 

Se si desidera creare nuove soluzioni di integrazione che riducano al minimo le operazioni di gestione dell'infrastruttura, usare Servizi BizTalk. Se si desidera eseguire la migrazione rapida delle soluzioni BizTalk esistenti o si cerca un ambiente su richiesta per sviluppare e testare applicazioni BizTalk Server, usare BizTalk Server su una macchina virtuale Azure.

#### Qual è la differenza tra BizTalk Adapter Service e le connessioni ibride?
BizTalk Adapter Service viene usato da un Servizio BizTalk di Azure. BizTalk Adapter Service usa BizTalk Adapter Pack per la connessione a un sistema line-of-business locale. Una connessione ibrida consente di connettere applicazioni Azure, come Siti Web e Servizi mobili, alle risorse locali in modo facile e intuitivo. 

#### Quando si crea un contratto in Servizi BizTalk, perché il numero di bridge aumenta di due anziché di uno? 

Ogni contratto include sue bridge diversi, uno per la comunicazione sul lato di invio e l'altro sul lato di ricezione.

####  Che cosa succede se si raggiunge il limite di quota o il numero massimo di bridge o contratti? 

Non è possibile distribuire nuovi bridge o creare nuovi contratti. Per distribuire più bridge e contratti, è necessario aumentare il numero di unità del servizio BizTalk oppure passare a un'edizione superiore.

#### Come si esegue la migrazione da un livello di Servizi BizTalk a un altro? 

Usare il flusso di backup e ripristino per eseguire la migrazione da un livello a un altro. Sono supportati solo alcuni percorsi di migrazione. Fare riferimento a [Servizi BizTalk: backup e ripristino](http://go.microsoft.com/fwlink/p/?LinkID=329873) per informazioni più dettagliate sui percorsi di migrazione supportati.

#### Il servizio Adapter BizTalk è incluso nel servizio? Come si riceve il software?

Sì, il servizio Adapter BizTalk e BizTalk Adapter Pack sono inclusi in nel [download](http://www.microsoft.com/download/details.aspx?id=39087) dell'SDK di Servizi BizTalk di Azure.

## Passaggi successivi

Per eseguire il provisioning di Servizi BizTalk di Azure nel portale di gestione di Azure, vedere l'articolo [Servizi BizTalk: Provisioning tramite il portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280). Per iniziare a creare applicazioni, vedere [Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## Vedere anche
- [Servizi BizTalk: provisioning tramite il portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Servizi BizTalk: grafico dello stato di provisioning](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [Servizi BizTalk: schede Dashboard, Monitor e Scale](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Servizi BizTalk: Backup e ripristino](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Servizi BizTalk: limitazione](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
- [Servizi BizTalk: nome e chiave dell'autorità emittente](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
- [Come iniziare a usare l'SDK di Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>

<!--HONumber=46--> 

<!--HONumber=46--> 

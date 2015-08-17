<properties 
   pageTitle="Creazione di un processo B2B nel servizio app di Microsoft Azure" 
   description="Panoramica sulla creazione di un processo Business to Business" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="07/01/2015"
   ms.author="rajram"/>


# Creazione di un processo B2B


## Scenario aziendale 
Contoso e Northwind sono partner commerciali. Contoso (il rivenditore) invia ordini di acquisto a Northwind (il fornitore) su un trasporto di livello aziendale, ad esempio AS2. Northwind archivia tutti gli ordini in ingresso nel proprio spazio di archiviazione cloud. Gli ordini di acquisto sono messaggi XML tra i due partner. Dopo l'archiviazione del messaggio nel cloud, l'ordine viene gestito dai processi interni di Northwind.
 
L'obiettivo di questa esercitazione è mostrare come Northwind può stabilire un processo aziendale mediante il quale ricevere messaggi (ordini di acquisto in formato XML) su AS2 dal suo partner Contoso e quindi salvarli in modo permanente nel proprio spazio di archiviazione cloud.


## Funzionalità illustrate 
Questa esercitazione illustra le funzionalità seguenti:

- **Trasporto dei messaggi**: il rivenditore e il fornitore possono essere su piattaforme diverse, ma possono comunque comunicare tra loro. In questa esercitazione comunicano tramite AS2 (Applicability Statement 2). AS2 è una diffusa specifica per lo scambio di dati tra partner commerciali nelle comunicazioni Business to Business.
- **Persistenza dei dati**: dopo la ricezione del messaggio tramite AS2, Northwind vuole salvarli in modo permanente prima dell'ulteriore elaborazione. Può servirsi di un connettore per rendere i messaggi persistenti nel proprio spazio di archiviazione cloud. In questa esercitazione, come archiviazione cloud per Northwind vengono usati i BLOB di Azure.
- **Creazione di un processo aziendale**: all'interno di un flusso è possibile riunire più app per le API per ottenere risultati simili a quello qui illustrato.


## Prima di iniziare
Questa esercitazione presuppone che l'utente abbia una conoscenza di base dei Servizi app di Azure e sia in grado di creare app per le API e comporre un flusso.


## Passaggi per la creazione dello scenario aziendale
**Creare e configurare le app per le API necessarie**

1. Creare un'istanza del **connettore BLOB di Archiviazione di Azure**. A questo scopo sono necessarie le credenziali di un account di archiviazione di Azure. Prima di iniziare, assicurarsi che sia disponibile.
2. Creare un'istanza dell'app per le **API BizTalk Trading Partner Management**. Per il funzionamento dell'app per le API è necessario un database SQL vuoto. Prima di iniziare, assicurarsi che sia disponibile.
3. Creare un'istanza del **connettore AS2**. Anche per il funzionamento di questo connettore è necessario un database SQL vuoto. Prima di iniziare, assicurarsi che sia disponibile. Inoltre, se si vogliono archiviare i messaggi come parte dell'elaborazione AS2, è possibile fornire le credenziali per un BLOB di Azure durante la creazione del connettore.
4. Configurare il servizio TPM (Trading Partner Management) da creare:
	1. Passare all'istanza del servizio TPM creato nel passaggio precedente.
	2. Usare l'opzione **Partner** in *Componenti* per **aggiungere** un nuovo partner di nome **Contoso** e aggiungere l'identità AS2 necessaria nel relativo profilo.
	3. Usare l'opzione **Partner** in *Componenti* per **aggiungere** un nuovo partner di nome **Northwind** e aggiungere l'identità AS2 necessaria nel relativo profilo.
	4. Usare l'opzione **Contratti** in *Componenti* per **aggiungere** un nuovo contratto AS2 tra Northwind e Contoso. Northwind sarà il partner ospitato e Contoso sarà il partner guest. Durante la creazione del contratto, configurare la firma, la compressione, la crittografia e i riconoscimenti come necessario. Se è necessario l'uso di certificati, è possibile caricarli mediante l'opzione **Certificati** quando si visualizza il servizio TPM creato.


## Creare un flusso/processo aziendale
1. Creare un nuovo flusso in cui il primo passaggio è AS2. Trascinare il **connettore AS2** e scegliere l'istanza già creata. Scegliere la funzionalità trigger

![][1]

2. Trascinare quindi il **connettore BLOB di Archiviazione di Azure** e scegliere l'istanza già creata. Scegliere la funzionalità azione e selezionare Carica BLOB. Configurare in base alle proprie esigenze

3. A questo punto, creare o distribuire il flusso


## Elaborazione dei messaggi e risoluzione dei problemi
1. È il momento di testare il flusso distribuito. Inviare messaggi XML con wrapping AS2 (in base al contratto AS2 creato in precedenza) all'endpoint AS2 presentato dall'istanza del connettore AS2 creata. Può essere necessario configurare l'autenticazione per l'endpoint in modo che sia pubblicamente accessibile.
2. Per accedere alle informazioni di esecuzione relative al flusso, passare al flusso e quindi all'istanza di flusso eseguita.
3. Per accedere alle informazioni di elaborazione AS2, passare all'istanza del connettore AS2 coinvolta e quindi alla sezione Rilevamento dell'istanza. È possibile applicare filtri per limitare la visualizzazione alle informazioni desiderate.

![][2]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-b2b-process/Flow.jpg
[2]: ./media/app-service-logic-create-a-b2b-process/Tracking.jpg
 

<!---HONumber=August15_HO6-->
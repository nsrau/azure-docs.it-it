<properties 
	pageTitle="Sicurezza di Operational Insights" 
	description="Operational Insights è un servizio di analisi che consente agli amministratori IT di ottenere una visione approfondita di ambienti locali e nel cloud. Consente di interagire con i dati dei computer, sia quelli cronologici che quelli in tempo reale, per lo sviluppo rapido di informazioni personalizzate, oltre a fornire modelli sviluppati da Microsoft e dalla community per l'analisi dei dati." 
	services="operational-insights" 
	documentationCenter="" 
	authors="bandersmsft" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="operational-insights" 
	ms.workload="appservices" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/06/2014" 
	ms.author="banders"/>





<h1 id="">Sicurezza di Operational Insights</h1>


Microsoft si impegna a proteggere la privacy e i dati degli utenti, mettendo a disposizione software e servizi che aiutino a gestire l'infrastruttura IT dell'organizzazione. Microsoft è a conoscenza della necessità di applicare rigorose misure di sicurezza quando si affidano i dati a terzi. Microsoft è conforme alle più rigorose linee guida sulla sicurezza e sulla conformità in tutte le fasi, dalla codifica all'esecuzione di un servizio.

La sicurezza e la protezione dei dati sono altamente prioritarie per Microsoft. Contattare Microsoft per qualsiasi domanda, suggerimento o problema in relazione a qualsiasi aspetto delle informazioni riportate in questo articolo, compresi i criteri di sicurezza: <a href="mailto:scdata@microsoft.com" target="_blank">scdata@microsoft.com</a>.

Questo articolo illustra il modo in cui i dati vengono raccolti, elaborati e protetti in Microsoft Azure Operational Insights. È possibile usare degli agenti per connettersi direttamente al servizio Web oppure è possibile usare System Center Operations Manager per raccogliere i dati operativi per il servizio Operational Insights. I dati raccolti vengono inviati tramite Internet al servizio Operational Insights, che è ospitato in Microsoft Azure.

Il servizio Operational Insights gestisce i dati in modo sicuro usando i metodi seguenti:

**Separazione dei dati:** i dati dei clienti vengono tenuti separati logicamente in ogni componente del servizio Operational Insights. Tutti i dati vengono contrassegnati in base all'organizzazione. Tale contrassegno persiste per tutto il ciclo di vita dei dati e viene applicato a ogni livello del servizio. 

Ogni cliente ha un BLOB di Azure dedicato che ospita i dati a lungo termine. Il BLOB viene crittografato con chiavi univoche per ogni cliente, che vengono modificate ogni 90 giorni.

**Conservazione dei dati:** le metriche aggregate per ognuno degli Intelligence Pack vengono archiviate in un Database SQL ospitato in Microsoft Azure. Questi dati vengono archiviati per 390 giorni. I dati per la ricerca indicizzata vengono archiviati in media per 10 giorni prima di essere eliminati. Se il limite massimo di 20 milioni di record per ogni tipo di dati viene raggiunto prima, Operational Insights eseguirà la pulitura dei dati prima che siano trascorsi i 10 giorni. Se il limite dei dati non viene raggiunto entro 10 giorni, Operational Insights attende fino al raggiungimento di tale limite prima di procedere alla pulitura.

**Sicurezza fisica:** il servizio Operational Insights è supportato da personale Microsoft, tutte le attività vengono registrate e possono essere controllate. Il servizio Operational Insights viene eseguito interamente in Azure e soddisfa i criteri di progettazione comuni di Azure. È possibile verificare i dettagli sulla sicurezza fisica delle risorse di Azure a pagina 18 del documento sulla  <a href="http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf" target="_blank">panoramica della sicurezza in Microsoft Azure</a>.

**Conformità e certificazioni:** il team dedicato allo sviluppo software e al servizio Operational Insights è impegnato attivamente nella collaborazione con il team legale di Microsoft, nonché con altri team preposti alla gestione della conformità e con altri partner di settore, per acquisire un'ampia gamma di certificazioni, tra cui ISO, prima che il servizio Operational Insights venga reso disponibile a livello generale.

Attualmente, il servizio è già conforme ai seguenti standard di sicurezza:

- Criteri di progettazione comuni di Windows
- Certificazione Microsoft Trustworthy Computing



<!--HONumber=45--> 

<properties 
   pageTitle="Considerazioni su Operations Manager con Operational Insights"
   description="Se si usa Microsoft Azure Operational Insights con Operations Manager, la configurazione si baserà su una distribuzione degli agenti e dei gruppi di gestione di Operations Manager per raccogliere e inviare dati al servizio Operational Insights per l'analisi."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/20/2015"
   ms.author="banders" />

# Considerazioni su Operations Manager con Operational Insights

Se si usa Microsoft Azure Operational Insights con Operations Manager, la configurazione si baserà su una distribuzione degli agenti e dei gruppi di gestione di Operations Manager per raccogliere e inviare dati al servizio Operational Insights per l'analisi. Se tuttavia si usano agenti che si connettono direttamente al servizio Web, Operations Manager non è necessario. Prendere in considerazione i problemi seguenti quando si usa Operational Insights con Operations Manager.

## Funzioni e requisiti software di Operational Insights

Operational Insights è costituito da un servizio Web nel cloud e da agenti che si connettono direttamente al servizio Web o da agenti Operations Manager e gruppi di gestione di Operations Manager che gestiscono i computer nell'ambiente.

Prima di selezionare agenti Operations Manager (per la raccolta dei dati) e gruppi di gestione (per la gestione degli agenti e l'invio dei dati al servizio Operational Insights), assicurarsi di avere compreso i concetti seguenti:

- L'agente Operations Manager viene installato in tutti i server da cui si vogliono raccogliere e analizzare dati.

- Il gruppo di gestione di Operations Manager trasferisce i dati dagli agenti al servizio Web di Operational Insights. Non esegue l'analisi dei dati.

- Il gruppo di gestione di Operations Manager deve avere accesso a Internet per caricare i dati nel servizio Web.

- Per risultati ottimali, non installare il server di gestione di Operations Manager in un computer già configurato come controller di dominio.

- Un agente Operations Manager deve disporre della connettività di rete al gruppo di gestione di Operations Manager per il trasferimento dei dati.

Per raccogliere e analizzare dati, Operational Insights può usare il Servizio integrità di System Center. Operations Manager dipende dal Servizio integrità di System Center. Quando si visualizzano i programmi installati nel server, il software dell'agente System Center Operations Manager risulterà elencato, in particolare in Installazione applicazioni. Non rimuovere questo software, perché Operational Insights dipende da esso. Se si rimuove il software dell'agente Operations Manager, Operational Insights non funzionerà più.

## Coesistenza con Operations Manager

Quando si usa Operations Manager, Operational Insights è supportato con l'agente Operations Manager solo in System Center Operations Manager 2012 R2 o System Center Operations Manager 2012 SP1. Non è supportato con le versioni precedenti di System Center Operations Manager. Poiché l'agente Operations Manager viene usato per raccogliere dati, usa credenziali specifiche (account azione o account RunAs) per supportare alcuni dei carichi di lavoro analizzati, ad esempio SharePoint 2012.

## Operational Insights e SQL Server 2012

Quando si usa Operations Manager, il Servizio integrità di System Center viene eseguito con l'account di sistema locale. Nelle versioni di SQL Server precedenti a SQL Server 2008 R2, l'account di sistema locale è abilitato per impostazione predefinita ed è membro del ruolo del server amministratore di sistema. In SQL Server 2012 l'accesso con l'account di sistema locale non fa parte del ruolo del server amministratore di sistema. Di conseguenza, quando si usa Operational Insights, l'istanza di SQL Server 2012 non può essere monitorata completamente e non tutte le regole possono generare avvisi.

## Connettività Internet e di rete interna

Quando si usano agenti che si connettono direttamente al servizio Web, questi devono poter accedere a Internet per inviare dati al servizio Web e per ricevere informazioni di configurazione aggiornate dal servizio Web.

Quando si usa Operations Manager, il server di gestione deve poter accedere a Internet per inviare dati al servizio Web e per ricevere informazioni di configurazione aggiornate dal servizio Web. Per gli agenti non è tuttavia necessario accedere a Internet. Se gli agenti Operations Manager sono installati in server non connessi a Internet, è possibile usare il servizio Web se sono in grado di comunicare con un server di gestione connesso a Internet.

## Supporto del clustering

L'agente Operations Manager è supportato nei computer che eseguono Windows Server 2012, Windows Server 2008 R2 o Windows Server 2008 configurato come parte di un cluster di failover di Windows. I cluster sono visibili nel portale di Operational Insights. Nella pagina relativa ai server, i cluster sono identificati come TYPE=CLUSTER (diversamente da TYPE=AGENT, che indica la modalità di identificazione dei computer fisici).

Le regole di individuazione e configurazione vengono eseguite nei nodi attivi e passivi del cluster, tuttavia gli avvisi generati nei nodi passivi vengono ignorati. Se un nodo passa da passivo ad attivo, i relativi avvisi vengono visualizzati automaticamente, senza alcun intervento da parte dell'utente.

Alcuni avvisi potrebbero essere generati due volte, a seconda della regola che genera l'avviso. Ad esempio, una regola che rileva un driver non valido esaminando il sistema operativo che genera gli avvisi per il server fisico e per il cluster.

L'analisi della configurazione dei nodi passivi non è supportata.

Operational Insights non supporta il raggruppamento o il collegamento di computer che eseguono Windows Server appartenenti allo stesso cluster di failover.

## Ridimensionamento dell'ambiente di Operational Insights

Quando si pianifica la distribuzione di Operational Insights, specialmente quando si analizza il numero di agenti Operations Manager che dovranno trasferire dati tramite un unico gruppo di gestione, considerare la capacità del server in termini di spazio per i file.

Considerare le variabili seguenti.

- Numero di agenti per gruppo di gestione.

- Dimensioni medie dei dati trasferiti dall'agente al gruppo di gestione ogni giorno. Per impostazione predefinita, ogni agente carica file CAB nel gruppo di gestione due volte al giorno. Le dimensioni dei file CAB dipendono della configurazione del server (ad esempio il numero di motori di SQL Server e il numero di database) e dall'integrità del server (ad esempio il numero di avvisi generati). Nella maggior parte dei casi, le dimensioni dei dati caricati giornalmente sono in genere meno di 100 KB.

- Periodo di archiviazione per la conservazione dei dati nel gruppo di gestione (il valore predefinito è di 5 giorni).

Ad esempio, presupponendo dimensioni di caricamento giornaliere di 100 KB per ogni agente e il periodo di archiviazione predefinito, per il gruppo di gestione sarà necessario lo spazio di archiviazione seguente:

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Numero di agenti</b></td>
		<td><b>Spazio stimato necessario per il gruppo di gestione</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>5</td>
		<td>~2,5 MB (5 agenti x 100 KB di dati/giorno x 5 giorni = 2.500 KB)</td>
    </tr>
    <tr align="left" valign="top">
		<td>50</td>
		<td>~25 MB (50 agenti x 100 KB di dati/giorno x 5 giorni = 25.000 KB)</td>
    </tr>

    </tbody>
    </table>

## Posizioni geografiche

Per analizzare i dati di server in diverse posizioni geografiche, considerare la configurazione di un gruppo di gestione per posizione. Questo approccio può migliorare le prestazioni per il trasferimento dei dati dall'agente al gruppo di gestione.


<!--HONumber=52-->
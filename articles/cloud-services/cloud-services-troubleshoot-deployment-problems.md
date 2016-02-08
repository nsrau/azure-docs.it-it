<properties
 pageTitle="Risolvere eventuali problemi di distribuzione dei servizi cloud | Microsoft Azure"
 description="Durante la distribuzione di un servizio cloud in Azure, è possibile che si verifichino problemi comuni. Questo articolo fornisce soluzioni per alcuni problemi."
   services="cloud-services"
   documentationCenter=""
   authors="dalechen"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="01/20/2016"
   ms.author="daleche" />

# Come risolvere eventuali problemi di distribuzione dei servizi cloud rilevati

Quando si distribuisce il pacchetto di un'applicazione di servizi cloud in Azure, è possibile ottenere informazioni sulla distribuzione nel riquadro **Proprietà** del portale di Azure. I dettagli riportati in questo riquadro possono essere usati per risolvere i problemi relativi al servizio cloud e queste informazioni possono essere fornite al supporto tecnico di Azure quando viene aperta una nuova richiesta di supporto.

È possibile trovare il riquadro **Proprietà** come indicato di seguito:

* Nel portale di Azure: fare clic sulla distribuzione del servizio cloud, selezionare **Tutte le impostazioni** e quindi **Proprietà**.
* Nel portale di Azure classico: fare clic sulla distribuzione del servizio cloud e selezionare **DASHBOARD** che si trova nell'angolo inferiore destro della pagina (in **Riepilogo rapido**). Tenere presente che il testo "Proprietà" è mancante in questo riquadro.

> [AZURE.NOTE] È possibile copiare il contenuto del riquadro Proprietà negli Appunti facendo clic sull'icona nell'angolo superiore destro del riquadro.

## Contattare l'assistenza clienti di Azure

Se in qualsiasi punto dell'articolo sono necessarie altre informazioni, è possibile contattare gli esperti di Azure nei [forum MSDN e overflow dello stack relativi ad Azure](https://azure.microsoft.com/support/forums/).

In alternativa, è anche possibile archiviare un evento imprevisto di supporto tecnico di Azure. Andare al [sito di supporto di Azure](https://azure.microsoft.com/support/options/) e fare clic su **Ottieni supporto**. Per informazioni sull'uso del supporto per Azure, vedere le [Domande frequenti sul supporto tecnico di Microsoft Azure](https://azure.microsoft.com/support/faq/).



## Problema: Non è possibile accedere al sito Web anche se la distribuzione è stata avviata e tutte le istanze del ruolo sono pronte.

Il collegamento URL del sito Web mostrato nel portale non include la porta. La porta predefinita per i siti Web è 80. Tuttavia, se l'applicazione è configurata per l'esecuzione in una porta diversa, è necessario aggiungere all'URL la porta corretta durante l'accesso al sito Web.

1. Nel portale di Azure fare clic sulla distribuzione del servizio cloud.
2. Nel riquadro **Proprietà** del portale di Azure, selezionare le porte per le istanze del ruolo (in Endpoint di input).
3. Se la porta non è *80*, aggiungere il valore di porta corretto all'URL quando si accede all'applicazione. Per specificare una porta non predefinita, digitare l'URL, seguito da due punti (:) e dal numero di porta senza spazi.

## Problema: Istanze del ruolo personalizzate riavviate senza intervento dell'utente.

La correzione del servizio si verifica automaticamente quando tramite Azure vengono rilevati nodi problematici e le istanze del ruolo vengono spostate in nodi nuovi. In tal caso, è possibile che le istanze del ruolo vengano riavviate automaticamente. Per determinare se si è verificata la correzione del servizio:

1. Nel portale di Azure fare clic sulla distribuzione del servizio cloud.
2. Nel riquadro **Proprietà** del portale di Azure, controllare le informazioni e determinare se la correzione del servizio si è verificata quando si è notato il riavvio dei ruoli.

I ruoli vengono riavviati approssimativamente una volta al mese durante gli aggiornamenti del sistema operativo host e del sistema operativo guest. Per altre informazioni, vedere il post di blog sui [riavvii delle istanze del ruolo in seguito agli aggiornamenti del sistema operativo](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx).

## Problema: Non è possibile eseguire uno scambio VIP e si riceve un errore

Non è possibile eseguire uno scambio VIP se è in corso l'aggiornamento di una distribuzione. Gli aggiornamenti di distribuzione si possono verificare automaticamente quando:

* È disponibile un nuovo sistema operativo guest e sono stati configurati gli aggiornamenti automatici
* Si verifica la correzione del servizio

Per determinare se uno scambio VIP viene impedito da un aggiornamento automatico:

1. Nel portale di Azure fare clic sulla distribuzione del servizio cloud.
2. Nel riquadro **Proprietà** del portale di Azure esaminare il valore di **Stato**. Se è **Pronto**, selezionare **Ultima operazione** per verificare se ne è stata eseguita una di recente che potrebbe impedire lo scambio VIP.
3. Ripetere i passaggi 1 e 2 per la distribuzione di produzione.
4. Se è in corso un aggiornamento automatico, attenderne il completamento prima di provare a eseguire lo scambio VIP.

## Problema: Viene eseguito il ciclo di un'istanza del ruolo tra Avviato, Inizializzazione in corso, Occupato e Arrestato.

Questa condizione potrebbe indicare un problema relativo al codice, al pacchetto o al file di configurazione dell'applicazione. Se true, è possibile osservare nel portale di Azure che lo stato cambia ogni pochi minuti, diventando ad esempio **Riciclo in corso**, **Occupato** o **Inizializzazione in corso**, a indicare un errore dell'applicazione che impedisce l'esecuzione dell'istanza del ruolo.

Per altre informazioni sulla risoluzione di questo problema, vedere il post di blog sui [dati di diagnostica del calcolo Azure PaaS] e [Problemi comuni che comportano il riciclo dei ruoli](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

## Problema: Arresto del funzionamento dell'applicazione

1. Nel portale di Azure fare clic sull'istanza del ruolo.
2. Nel riquadro **Proprietà** del portale di Azure si considerino le condizioni seguenti per risolvere il problema:
   * Se l'istanza del ruolo è stata arrestata di recente (si può controllare il valore di **Conteggio interruzioni**), la distribuzione potrebbe essere in fase di aggiornamento. Attendere per verificare se il funzionamento dell'istanza del ruolo riprende autonomamente.
   * Se l'istanza del ruolo è occupata, controllare il codice dell'applicazione per vedere se l'evento [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck) è gestito. Potrebbe essere necessario aggiungere o correggere il codice mediante il quale viene gestito l'evento.
   * Esaminare i dati diagnostici e gli scenari per la risoluzione di problemi nel post di blog relativo ai [dati di diagnostica del calcolo Azure PaaS].

>[AZURE.WARNING] Se si riavvia il servizio cloud, si reimpostano le proprietà per la distribuzione, cancellando effettivamente le informazioni per il problema originale.

## Passaggi successivi

Altri [articoli sulla risoluzione dei problemi](..\?tag=top-support-issue&service=cloud-services) per i servizi cloud.

Per informazioni su come risolvere i problemi dei ruoli del servizio cloud usando i dati di diagnostica del calcolo Azure PaaS, vedere la [serie di blog di Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

<!---HONumber=AcomDC_0128_2016-->
<properties 
	pageTitle="Distribuire un servizio cloud in un ambiente di gestione temporanea (Node.js) - Azure" 
	description="Informazioni su come distribuire l'applicazione di Azure prima in un ambiente di gestione temporanea e quindi in un ambiente di produzione tramite uno scambio di IP virtuali (VIP, Virtual IP)." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="wpickett"/>







# Gestione temporanea di un'applicazione in Azure

È possibile distribuire nell'ambiente di gestione temporanea di
Azure un'applicazione inclusa in un pacchetto per la verifica prima dello spostamento
nell'ambiente di produzione in cui l'applicazione risulterà accessibile su Internet. L'ambiente
di gestione temporanea è identico all'ambiente di produzione, ma
ma è possibile accedere all'applicazione di gestione temporanea solo tramite un URL offuscato
generato da Azure. Dopo la verifica
del funzionamento corretto dell'applicazione, sarà possibile distribuirla nell'ambiente di produzione,
tramite uno scambio di indirizzi IP virtuali (VIP, Virtual IP).

> [AZURE.NOTE] I passaggi illustrati in questo articolo riguardano esclusivamente le applicazioni Node ospitate come Servizio cloud di Azure.

Questa attività include i seguenti passaggi:

-   [Passaggio 1: Gestire un'applicazione in modo temporaneo]
-   [Passaggio 2: Aggiornare un'applicazione in produzione tramite lo scambio di indirizzi VIP]

<h2><a id="step1"></a>Passaggio 1: Gestire un'applicazione in modo temporaneo</h2>

In questa attività viene illustrato come gestire un'applicazione in modo temporaneo mediante **Microsoft
Azure PowerShell**.

1.  Quando si pubblica un servizio, è sufficiente passare il parametro **-Slot**
    al cmdlet **Publish-AzureServiceProject**.

    **Publish-AzureServiceProject -Slot di gestione temporanea**

2.  Accedere al [portale di gestione di Azure] e selezionare **Servizi cloud**. Dopo la creazione del servizio cloud e l'aggiornamento dello stato della colonna **Gestione temporanea** in **In esecuzione**, fare clic sul nome del servizio.

	![portal displaying a running service][cloud-service]

3.  Selezionare il **Dashboard** e quindi selezionare **Gestione temporanea**.

	![cloud service dashboard][cloud-service-dashboard]

4. Prendere nota del valore della voce **URL sito** a destra. Il nome DNS è un ID interno offuscato generato da Azure.

    ![site url][cloud-service-staging-url]

È ora possibile verificare il corretto funzionamento dell'applicazione nell'ambiente di gestione temporanea, usando l'URL del sito di gestione temporanea.

Per uno scenario di aggiornamento in cui l'applicazione di gestione temporanea è una versione aggiornata,
di un'applicazione già distribuita in produzione, è possibile
[aggiornare l'applicazione nell'ambiente di produzione tramite lo scambio di indirizzi
VIP][Passaggio 2: Aggiornare un'applicazione in produzione tramite lo scambio di indirizzi VIP].

<h2><a id="step2"></a>Passaggio 2: Aggiornare un'applicazione in produzione tramite lo scambio di indirizzi VIP</h2>

Dopo la verifica della versione aggiornata di un'applicazione
nell'ambiente di gestione temporanea, sarà possibile renderla rapidamente disponibile in produzione tramite
lo scambio degli indirizzi VIP degli ambienti di gestione temporanea
e di produzione.

> [AZURE.NOTE] In questo passaggio si presuppone che un'applicazione sia già stata distribuita
in produzione e che sia già stata eseguita la gestione temporanea della versione aggiornata
dell'applicazione.

1.  Accedere al [portale di gestione di Azure],
    fare clic su **Servizi cloud** e quindi selezionare il nome del servizio.

2.  Da **Dashboard** selezionare **Gestione temporanea** e quindi fare clic su **Scambia** nella parte inferiore della pagina. Verrà aperta la finestra di dialogo
    Scambio VIP.

    ![vip swap dialog][vip-swap-dialog]

3.  Verificare le informazioni e quindi fare clic su **OK**. Verrà avviato
    l'aggiornamento delle due distribuzioni, mentre la distribuzione di gestione temporanea passa in produzione e
    la distribuzione di produzione passa in gestione temporanea.

La gestione temporanea di una distribuzione e l'aggiornamento di una distribuzione di produzione sono stati eseguiti correttamente
tramite lo scambio di indirizzi IP virtuali con la distribuzione in gestione temporanea.

## Risorse aggiuntive

- [Come distribuire un aggiornamento del servizio nella produzione scambiando gli indirizzi VIP in Azure]
- [Informazioni generali sulla gestione delle distribuzioni in Azure]

  [Passaggio 1: Gestire un'applicazione in modo temporaneo]: #step1
  [Passaggio 2: Aggiornare un'applicazione in produzione tramite lo scambio di indirizzi VIP]: #step2
  [Portale di gestione di Azure]: http://manage.windowsazure.com
[cloud-service]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
[cloud-service-dashboard]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
  [cloud-service-staging-url]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
  [vip-swap-dialog]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
  [Come distribuire un aggiornamento del servizio nella produzione scambiando gli indirizzi VIP in Azure]: http://msdn.microsoft.com/library/windowsazure/ee517253.aspx
  [Informazioni generali sulla gestione delle distribuzioni in Azure]: http://msdn.microsoft.com/library/windowsazure/hh386336.aspx

<!--HONumber=45--> 

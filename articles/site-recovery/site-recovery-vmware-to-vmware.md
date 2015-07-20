<properties 
	pageTitle="Eseguire la distribuzione tra siti VMWare locali" 
	description="In Azure Site Recovery, InMage Scout gestisce la replica, il failover e il ripristino tra siti VMWare locali." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/29/2015" 
	ms.author="raynew"/>


# Eseguire la distribuzione tra siti VMWare locali


##Panoramica

In Azure Site Recovery, InMage Scout fornisce la replica in tempo reale tra più siti VMWare locali. InMage Scout è incluso nelle sottoscrizioni del servizio Azure Site Recovery.


## Prerequisiti

- **Account Azure**: sarà necessario disporre di un account [Microsoft Azure](http://azure.microsoft.com/). È possibile iniziare con una [versione di valutazione gratuita](pricing/free-trial/).


##Passaggio 1: creare un insieme di credenziali e scaricare InMage Scout

1. Accedere al [portale di gestione](https://portal.azure.com).
2. Espandere **Servizi dati** > **Servizi di ripristino** > **Insieme di credenziali di Site Recovery**.
3. Fare clic su **Crea nuovo** > **Creazione rapida**.
4. In **Nome** immettere un nome descrittivo per identificare l'insieme di credenziali.
5. In **Area** selezionare l'area geografica per l'insieme di credenziali. Per verificare le aree geografiche supportate, vedere la sezione Disponibilità a livello geografico in [Prezzi di Azure Site Recovery](pricing/details/site-recovery/).

<P>Controllare la barra di stato per verificare che l'insieme di credenziali sia stato creato correttamente. L'insieme di credenziali verrà elencato come <b>Attivo</b> nella pagina principale di Servizi di ripristino.</P>

##Passaggio 2: configurare l'insieme di credenziali

1. Fare clic su **Create vault**.
2. Nella pagina **Servizi di ripristino** fare clic sull'insieme di credenziali per aprire la pagina Avvio rapido.
3. Dall'elenco a discesa selezionare **Tra due siti VMWare locali**.
4. Scaricare InMage Scout.
5. Impostare la replica tra due siti VMWare facendo riferimento alla documentazione di InMage Scout scaricata con il prodotto.


##Passaggi successivi

In caso di domande, è possibile pubblicarle nel [forum relativo ai Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).<

<!---HONumber=July15_HO2-->
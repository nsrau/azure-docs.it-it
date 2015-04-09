<properties 
	pageTitle="Esercitazione: Configurare la protezione tra siti VMWare locali" 
	description="In Azure Site Recovery InMage gestisce la replica, il failover e il ripristino tra siti VMWare locali." 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="raynew"/>

# Esercitazione: Configurare la protezione tra siti VMWare locali


<h2><a id="overview" name="overview" href="#overview"></a>Informazioni generali</h2>

<p>In Azure Site Recovery, InMage fornisce la replica in tempo reale tra più siti VMWare locali. InMage è incluso nelle sottoscrizioni del servizio Azure Site Recovery.</p>




<h2><a id="before" name="before" href="#before"></a>Prerequisiti</h2>
<div class="dev-callout"> 

<UL>
<LI><b>Account di Azure</b>: è necessario disporre di un account di Azure. Se non si ha un account, vedere la pagina relativa alla <a href="http://aka.ms/try-azure">versione di prova gratuita di Azure</a>. Per informazioni sui prezzi della sottoscrizione, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Prezzi di Azure Site Recovery</a>.</LI>
</UL>


<h2><a id="tutorial" name="tutorial" href="#tutorial"></a>Passaggi dell'esercitazione</h2>
<a name="vault"></a> <h3>Passaggio 1: Creare un insieme di credenziali e scaricare InMage</h3>

1. Accedere al [portale di gestione](https://manage.windowsazure.com).


2. Espandere <b>Servizi dati</b>, quindi <b>Servizi di ripristino</b>e fare clic su <b>Insieme di credenziali di Azure Site Recovery</b>.


3. Fare clic su <b>Crea nuovo</b> e quindi su <b>Creazione rapida</b>.
	
4. In <b>Nome</b> immettere un nome descrittivo per identificare l'insieme di credenziali.

5. In <b>Area</b> selezionare l'area geografica per l'insieme di credenziali. Per verificare le aree geografiche supportate, vedere la sezione Disponibilità a livello geografico nell'articolo <a href="http://go.microsoft.com/fwlink/?LinkId=389880">Prezzi di Azure Site Recovery</a>.

6. Fare clic su <b>Crea insieme di credenziali</b>. 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SRSAN_HvVault.png)

<P>Controllare la barra di stato per verificare che l'insieme di credenziali sia stato creato correttamente. L'insieme di credenziali verrà elencato come <b>Attivo</b> nella pagina principale di Servizi di ripristino.</P>

<a name="upload"></a> <h3>Passaggio 2: Configurare l'insieme di credenziali</h3>


1. Nella pagina <b>Servizi di ripristino</b> fare clic sull'insieme di credenziali per aprire la pagina Avvio rapido. La pagina Avvio rapido può anche essere aperta in qualsiasi momento tramite l'icona.

	![Quick Start Icon](./media/hyper-v-recovery-manager-configure-vault/SRSAN_QuickStartIcon.png)

2. Dall'elenco a discesa selezionare **Tra due siti VMWare locali**.
3. Scaricare InMage Scout.
	
	![VMWare](./media/hyper-v-recovery-manager-configure-vault/SRVMWare_SelectScenario.png)

4. Impostare la replica tra due siti VMWare facendo riferimento alla documentazione di InMage scaricata con il prodotto.


<h2><a id="next" name="next" href="#next"></a>Passaggi successivi</h2>
<UL>

<LI>In caso di domande, vedere il <a href="http://go.microsoft.com/fwlink/?LinkId=313628">forum relativo ai Servizi di ripristino di Azure</a>.</LI> 
</UL>

<!--HONumber=49-->
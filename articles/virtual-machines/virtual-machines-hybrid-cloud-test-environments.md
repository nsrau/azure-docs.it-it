<properties
	pageTitle="Ambienti di test cloud ibridi in Azure | Microsoft Azure"
	description="Collegamenti ad articoli che illustrano come creare ambienti di sviluppo/test o ambienti informatici professionali proof-of-concept per il cloud ibrido basato su Azure."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="index-page"
	ms.date="09/16/2015"
	ms.author="josephd"/>

# Ambienti di test basati su cloud ibrido

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]In questo articolo viene illustrata la creazione delle risorse con il modello di distribuzione classica.

Per sviluppo o a scopo di test, gli ambienti di test basati su cloud ibrida utilizzano la connessione a Internet locale e uno degli indirizzi IP pubblici e consentono un’impostazione semplice e dettagliata per l'impostazione di una funzione, rete virtuale di Azure di più sedi locali (VNet) cross-premise. Al termine, è possibile sviluppare ed effettuare il test di applicazioni, sperimentare con carichi di lavoro IT semplificati e misurare le prestazioni di una connessione di rete privata virtuale (VPN, Virtual Private Network) site-to-site relativa alla propria posizione su Internet.

## Configurazione di base del cloud ibrido

La [configurazione di base di un cloud ibrido](../virtual-network/virtual-networks-setup-hybrid-cloud-environment-testing.md) è costituita da quanto segue:

- Una rete locale semplificata con quattro macchine virtuali (ad esempio un controller di dominio, un server di applicazioni, un computer client e un dispositivo VPN che esegue Windows Server e routing e Remote Access)
- Una rete virtuale di Azure con un controller di dominio di replica
- Una connessione VPN site-to-site.

## Farm Intranet di SharePoint in un cloud ibrido

La [farm intranet di SharePoint intranet in un ambiente di test basato cloud ibrido](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md) include un server SQL Server 2014 e un server di SharePoint Server 2013 per la configurazione di base del cloud ibrido. Consente di creare una farm di SharePoint a due livelli a cui è possibile accedere dal computer client nella rete locale semplificata.

## Applicazione line-of-business (LOB) basata su Web in un cloud ibrido

L’[applicazione LOB basata sul Web in un ambiente di test in un cloud ibrido](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md) include un server SQL Server 2014 e un server Internet Information Services (IIS) per la configurazione di base del cloud ibrido. Ciò crea l'infrastruttura in cui è possibile distribuire e testare un'applicazione LOB basata su Web, a più livelli.

## Server di sincronizzazione della directory (DirSync) di Office 365

Il [server di Office 365 DirSync in un ambiente cloud ibrido](../virtual-network/virtual-networks-setup-dirsync-hybrid-cloud-testing.md) include un server DirSync per la configurazione di base del cloud ibrido e fornisce una dimostrazione di Office 365 DirSync con sincronizzazione password a una relativa una sottoscrizione di valutazione Office 365.

## Ambiente di test basato su cloud ibrido simulato

Per le organizzazioni e individui per cui una connessione diretta a Internet e l'indirizzo IP pubblico non sono immediatamente disponibili, l’[ambiente di test basato su cloud ibrido simulato](../virtual-network/virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md) consente di implementare la rete locale semplificata in una rete virtuale di Azure separata, quindi consente di connettere le due reti virtuali con una connessione VPN VNet-to-VNet.


## Risorse aggiuntive

[Farm di SharePoint ospitate nei servizi di infrastruttura di Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Carico di lavoro dei servizi di infrastruttura di Azure: applicazione line-of-business a disponibilità elevata](virtual-machines-workload-high-availability-LOB-application.md)

[Distribuire la sincronizzazione delle directory di Office 365 (DirSync) in Microsoft Azure](https://technet.microsoft.com/library/dn635310.aspx)

[Linee guida sull'implementazione dei servizi di infrastruttura di Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

<!---HONumber=Sept15_HO3-->
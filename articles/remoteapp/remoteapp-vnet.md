
<properties
    pageTitle="Convalidare la rete virtuale di Azure da usare con Azure RemoteApp | Microsoft Azure"
    description="Informazioni su come assicurarsi che la rete virtuale di Azure sia pronta all’utilizzo con Azure RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2015"
    ms.author="elizapo" />



# Convalidare la rete virtuale di Azure da usare con Azure RemoteApp

Prima di utilizzare una rete virtuale di Azure con Azure RemoteApp, è possibile convalidare la rete virtuale. Consente di evitare problemi con la connettività.

Per convalidare la rete virtuale di Azure, procedere come segue:

1. Creare una macchina virtuale di Azure all'interno della subnet della rete virtuale di Azure da usare con Azure RemoteApp.

2. Connettersi a tale macchina virtuale usando l’opzione **Connetti** nel portale di gestione.
3. Aggiungere la macchina virtuale allo stesso dominio che si desidera usare con Azure RemoteApp. Se si sta creando una raccolta ibrida che si connette alla rete locale, aggiungere la macchina virtuale al dominio locale.

Se l'operazione viene completata, la rete virtuale di Azure è pronta per l'utilizzo con RemoteApp.

Per ulteriori informazioni sul flusso di lavoro della raccolta ibrida end-to-end, vedere gli articoli seguenti:

- [Come pianificare la rete virtuale per RemoteApp di Azure](remoteapp-planvpn.md)
- [Creare una raccolta ibrida](remoteapp-create-hybrid-deployment.md)
- [Distribuire la raccolta di Azure RemoteApp alla rete virtuale di Azure (con supporto per ExpressRoute)](http://blogs.msdn.com/b/rds/archive/2015/04/23/deploy-azure-remoteapp-collection-to-your-azure-virtual-network-with-support-for-expressroute.aspx)
 

<!---HONumber=Oct15_HO1-->
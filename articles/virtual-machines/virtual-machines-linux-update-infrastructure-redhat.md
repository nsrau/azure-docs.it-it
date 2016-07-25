<properties
   pageTitle="Red Hat Update Infrastructure (RHUI) | Microsoft Azure"
   description="Informazioni sul servizio Red Hat Update Infrastructure (RHUI) per istanze di Red Hat Enterprise Linux in Microsoft Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="BorisB2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="07/11/2016"
   ms.author="borisb"/>

# Red Hat Update Infrastructure (RHUI) per macchine virtuali Red Hat Enterprise Linux su richiesta in Azure

Le macchine virtuali create dalle immagini di Red Hat Enterprise Linux (RHEL) su richiesta disponibili in Azure Marketplace vengono registrate per accedere al servizio Red Hat Update Infrastructure (RHUI) distribuito in Azure. Le istanze di RHEL su richiesta potranno accedere a repository yum a livello di area e potranno ricevere aggiornamenti incrementali.

L'elenco di repository yum, gestito da RHUI, viene configurato nell'istanza di RHEL durante il provisioning. Non è necessario eseguire altre attività di configurazione. Per ottenere gli aggiornamenti più recenti, è sufficiente eseguire `yum update` quando l'istanza di RHEL è in esecuzione.

## Panoramica di RHUI
[Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) offre una soluzione altamente scalabile per gestire il contenuto del repository yum per le istanze cloud di Red Hat Enterprise Linux ospitate dai provider cloud certificati da Red Hat. In base al progetto Pulp upstream, RHUI consente ai provider cloud di eseguire il mirroring locale del contenuto del repository ospitato da Red Hat e creare repository personalizzati con il proprio contenuto e rendere disponibili tali repository per un ampio gruppo di utenti finali tramite un sistema di distribuzione del contenuto con bilanciamento del carico.

## Aree in cui viene è disponibile l'infrastruttura RHUI
Il servizio RHUI è disponibile in tutte le aree in cui sono disponibili le immagini di RHEL su richiesta. Include attualmente tutte le aree pubbliche elencate nella pagina [Stato di Azure](https://azure.microsoft.com/status/). L'accesso al servizio RHUI per macchine virtuali sottoposte a provisioning da immagini di RHEL su richiesta è incluso nel rispettivo prezzo. La disponibilità locale/nazionale del cloud verrà aggiornata in base all'espansione della disponibilità di RHEL su richiesta in futuro.

> [AZURE.NOTE] L'accesso all'istanza di RHUI ospitata in Azure è limitata alle macchine virtuali comprese negli [intervalli di indirizzi IP dei data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

## Ottenere aggiornamenti da un altro repository di aggiornamenti

Se è necessario ottenere aggiornamenti da un repository di aggiornamenti diverso, invece del servizio RHUI ospitato in Azure, sarà necessario annullare la registrazione delle istanze da RHUI e ripeterne la registrazione nell'infrastruttura di aggiornamento desiderata, ad esempio Red Hat Satellite o Red Hat Customer Portal CDN. Saranno necessarie sottoscrizioni di Red Hat appropriate per questi servizi e una registrazione per il programma [Red Hat Cloud Access in Azure](https://access.redhat.com/ecosystem/partners/ccsp/microsoft-azure).

Per annullare la registrazione a RHUI e registrarsi all'infrastruttura di aggiornamento desiderata, seguire questa procedura.

1.	Modificare /etc/yum.repos.d/rh-cloud.repo e cambiare tutti gli elementi `enabled=1` in `enabled=0`. Ad esempio:

        # sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo

2.	Modificare /etc/yum/pluginconf.d/rhnplugin.conf e cambiare tutti gli elementi `enabled=0` in `enabled=1`.
3.	Eseguire quindi la registrazione all'infrastruttura desiderata, ad esempio il portale dei clienti di Red Hat. Per informazioni su come [eseguire la registrazione e la sottoscrizione di un sistema nel portale dei clienti di Red Hat](https://access.redhat.com/solutions/253273), vedere la guida della soluzione Red Hat.

> [AZURE.NOTE] L'accesso al servizio RHUI ospitato in Azure è incluso nel prezzo dell'immagine di RHEL con pagamento in base al consumo. L'annullamento della registrazione di una macchina virtuale RHEL con pagamento in base al consumo dal servizio RHUI ospitato in Azure non converte la macchina virtuale in una VM di tipo Bring Your Own License (BYOL). È quindi possibile che vengano applicati addebiti doppi se si registra la stessa macchina virtuale in un'altra origine di aggiornamenti.
> 
> Se è necessario usare in modo continuativo un'infrastruttura di aggiornamento diversa dal servizio RHUI ospitato in Azure, prendere in considerazione la creazione e la distribuzione di immagini personalizzate di tipo BYOL, come descritto nell'articolo [Preparare una macchina virtuale basata su RedHat per Azure](virtual-machines-linux-redhat-create-upload-vhd.md).

## Passaggi successivi
Per creare una macchina virtuale Linux Red Hat Enterprise dall'immagine con pagamento in base al consumo di Azure Marketplace e sfruttare i vantaggi del servizio RHUI ospitato in Azure, passare ad [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/). Sarà possibile usare `yum update` nell'istanza di RHEL senza impostazioni aggiuntive.

<!---HONumber=AcomDC_0713_2016-->
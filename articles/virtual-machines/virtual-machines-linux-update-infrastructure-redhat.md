<properties
   pageTitle="Infrastruttura di aggiornamento per le immagini di Red Hat Enterprise Linux | Microsoft Azure"
   description="Introduce il servizio di aggiornamento yum per un'istanza di Red Hat Enterprise Linux su richiesta in Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="03/18/2016"
   ms.author="kyliel"/>

# Infrastruttura di aggiornamento per le immagini di Red Hat Enterprise Linux

Tramite Red Hat Update Infrastructure (RHUI) in Azure, è possibile gestire il contenuto del repository yum per le immagini di Azure Red Hat Enterprise Linux (RHEL). Le istanze RHEL su richiesta create da Azure Marketplace avranno quindi accesso al repository yum dell'area. In questo modo, le istanze RHEL potranno ricevere aggiornamenti incrementali.

L'elenco di repository yum, gestito da RHUI, viene configurato nell'istanza di RHEL durante il provisioning. Non sarà pertanto necessario eseguire altre attività di configurazione, ma semplicemente `yum update` dopo l'esecuzione dell'istanza RHEL.

## Panoramica di RHUI
[Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) offre una soluzione altamente scalabile per gestire il contenuto del repository yum per le istanze cloud di Red Hat Enterprise Linux ospitate dai provider cloud certificati da Red Hat. In base al progetto Pulp upstream, RHUI consente ai provider cloud di eseguire il mirroring locale del contenuto del repository ospitato da Red Hat e creare repository personalizzati con il proprio contenuto e rendere disponibili tali repository per un ampio gruppo di utenti finali tramite un sistema di distribuzione del contenuto con bilanciamento del carico.

## Aree in cui viene è disponibile l'infrastruttura RHUI
L'infrastruttura RHUI è disponibile in tutte le aree pubbliche elencate nel [dashboard dello stato di Azure](https://azure.microsoft.com/status/). Ciò significa che è possibile ottenere il servizio di aggiornamento yum senza alcun costo aggiuntivo in queste aree. Tali informazioni verranno aggiornate in futuro.

## Ottenere aggiornamenti da altri repository, ad esempio Red Hat Network Satellite

Per ottenere aggiornamenti da altri repository di aggiornamento occorre una licenza di Red Hat Cloud Access e una sottoscrizione Red Hat esistente ad Azure.

È quindi necessario annullare la registrazione a RHUI e ripetere la registrazione all'infrastruttura di aggiornamento. Di seguito sono illustrati i passaggi dettagliati.

1.	Modificare /etc/yum.repos.d/rh-cloud.repo e impostare tutte le occorrenze di `enabled=1` su `enabled=0`. Ad esempio:

        # sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo

2.	Modificare /etc/yum/pluginconf.d/rhnplugin.conf e impostare `enabled=0` su `enabled=1`.
3.	Quindi, registrarsi a Red Hat Network (RHN).

        rhn_register

    oppure

        rhnreg_ks


> [AZURE.NOTE] Verranno addebitati i trasferimenti di dati in uscita. Vedere i [dettagli sui prezzi](http://azure.microsoft.com/pricing/details/data-transfers/). Per ottenere gli aggiornamenti incrementali senza incorrere in costi aggiuntivi, è consigliabile usare l'infrastruttura RHUI predefinita.

## Passaggi successivi
Dopo aver appreso le caratteristiche di RHUI in Azure, è possibile creare un'immagine RHEL da [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/) e usare `yum update` nell'istanza di RHEL.

<!---HONumber=AcomDC_0323_2016-->
<properties 
   pageTitle="Che cos'è Gestione traffico | Microsoft Azure"
   description="Questo articolo fornisce informazioni su Gestione traffico e permette di determinare se sia la scelta giusta per il routing del traffico dell'applicazione"
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/09/2016"
   ms.author="sewhee" />  

# Gestione traffico di Azure

Gestione traffico di Microsoft Azure permette di controllare la distribuzione del traffico utente agli endpoint di servizio in esecuzione in diversi data center in tutto il mondo.

Gli endpoint di servizio supportati da Gestione traffico includono servizi cloud, app Web e macchine virtuali di Azure. È anche possibile usare Gestione traffico con endpoint esterni, non di Azure.

Gestione traffico usa il DNS (Domain Name System) per indirizzare le richieste degli utenti finali agli endpoint appropriati, in base al metodo di routing del traffico configurato e alla visualizzazione corrente dell'integrità dell'endpoint. I client si connettono quindi direttamente all'endpoint di servizio appropriato.

Gestione traffico supporta [diversi metodi di routing del traffico](traffic-manager-routing-methods.md) per soddisfare esigenze dell'applicazione di vario tipo. Gestione traffico offre [controlli di integrità e failover automatico degli endpoint](traffic-manager-monitoring.md), per compilare applicazioni a disponibilità elevata che siano resilienti in caso di errore, compreso il malfunzionamento di un'intera area di Azure.

## Vantaggi di Gestione traffico

Con Gestione traffico è possibile:

- **Migliorare la disponibilità delle applicazioni critiche**: Gestione traffico permette di garantire la disponibilità elevata delle applicazioni critiche attraverso il monitoraggio degli endpoint in Azure e il failover automatico in caso di inattività di un endpoint.
- **Migliorare la velocità di risposta per applicazioni a prestazioni elevate**: Azure consente di eseguire servizi cloud o siti Web in data center dislocati in tutto il mondo. Gestione traffico può migliorare la velocità di risposta delle applicazioni indirizzando gli utenti finali agli endpoint con la minore latenza di rete dal client.
- **Eseguire l'aggiornamento e la manutenzione dei servizi senza tempi di inattività**: usando Gestione traffico per indirizzare il traffico a endpoint alternativi durante la manutenzione, è possibile eseguire aggiornamenti e altre operazioni di manutenzione pianificate sulle applicazioni senza tempi di inattività per gli utenti finali.
- **Combinare applicazioni locali e applicazioni basate sul cloud**: Gestione traffico supporta anche endpoint esterni, non di Azure, e può quindi essere usato con distribuzioni cloud ibride e locali, inclusi gli scenari di tipo "burst nel cloud", "migrazione nel cloud" o "failover nel cloud".
- **Indirizzare il traffico per distribuzioni complesse di grandi dimensioni**: è possibile combinare i metodi di routing del traffico usando i [profili annidati di Gestione traffico](traffic-manager-nested-profiles.md) per creare configurazioni di routing del traffico avanzate e flessibili e soddisfare le esigenze di distribuzioni complesse di grandi dimensioni.

[AZURE.INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## Passaggi successivi

- Altre informazioni sul [funzionamento di Gestione traffico](traffic-manager-how-traffic-manager-works.md).

- Informazioni su come sviluppare applicazioni a disponibilità elevata usando il [monitoraggio degli endpoint di Gestione traffico](traffic-manager-monitoring.md).

- Altre informazioni sui [metodi di routing del traffico](traffic-manager-routing-methods.md) supportati da Gestione traffico.

- [Creare un profilo di Gestione traffico](traffic-manager-manage-profiles.md).
 

<!---HONumber=AcomDC_0824_2016-->
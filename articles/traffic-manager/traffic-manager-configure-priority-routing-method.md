---
title: "Configurare un metodo di routing del traffico Priorità con Gestione traffico di Azure | Microsoft Docs"
description: "In questo articolo viene descritto come configurare il metodo di routing del traffico Priorità tramite Gestione traffico di Azure"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 6dca6de1-18f7-4962-bd98-6055771fab22
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: kumud
ms.openlocfilehash: 0db83cde6facc89b8b8aa72e6419129ec868235c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-priority-traffic-routing-method-in-traffic-manager"></a>Configurare il metodo di routing del traffico Priorità di Gestione traffico

Indipendentemente dalla modalità del sito Web, i siti Web di Azure forniscono funzionalità di failover per i siti Web che si trovano in un data center (noto anche come area). Gestione traffico fornisce failover per i siti Web in diversi data center.

Un modello comune per il failover del servizio consiste nell'invio di traffico a un servizio primario e nel fornire un set di servizi di backup identici per il failover. I passaggi seguenti illustrano come configurare il failover con priorità con siti Web e servizi cloud di Azure:

## <a name="to-configure-the-priority-traffic-routing-method"></a>Per configurare il metodo di routing del traffico Priorità

1. Da un browser accedere al [portale di Azure](http://portal.azure.com). Se non si ha già di un account, è possibile iscriversi per ottenere una [versione di valutazione gratuita della durata di un mese](https://azure.microsoft.com/free/). 
2. Nella barra di ricerca del portale cercare i **profili di Gestione traffico** e quindi fare clic sul nome di profilo per cui si vuole configurare il metodo.
3. Nel pannello **Profilo di Gestione traffico** verificare che siano presenti sia i servizi cloud che i siti Web che si intende includere nella configurazione.
4. Nella sezione **Impostazioni** fare clic su **Configurazione** e nel pannello **Configurazione** procedere come indicato di seguito:
    1. Nelle **impostazioni del metodo di routing del traffico** verificare che il metodo di routing del traffico sia **Priorità**. In caso contrario, fare clic su **Priorità** nell'elenco a discesa.
    2. Specificare le stesse le stesse **impostazioni di monitoraggio degli endpoint** per tutti gli endpoint in questo profilo come indicato di seguito:
        1. Selezionare il **protocollo** appropriato e specificare il numero di **porta**. 
        2. In **Percorso** immettere una barra */*. Per monitorare gli endpoint, è necessario specificare un percorso e un nome file. Una barra ("/") è una voce valida per il percorso relativo e implica che il file si trovi nella directory radice (impostazione predefinita).
        3. Nella parte superiore della pagina fare clic su **Salva**.
5. Nella sezione **Impostazioni** fare clic su **Endpoint**.
6. Nel pannello **Endpoint** esaminare l'ordine di priorità per gli endpoint. Quando si seleziona il metodo di routing del traffico **Priorità**, l'ordine degli endpoint selezionati è rilevante. Verificare l'ordine di priorità degli endpoint.  L'endpoint primario si trova nella parte superiore. Verificare l'ordine di visualizzazione. Tutte le richieste vengono instradate al primo endpoint e, se Gestione traffico rileva che tale endpoint non è integro, viene eseguito automaticamente il failover del traffico sull'endpoint successivo. 
7. Per modificare l'ordine di priorità dell'endpoint, fare clic sull'endpoint e nel pannello **Endpoint** visualizzato fare clic su **Modifica** e cambiare il valore di **Priorità** in base alle esigenze. 
8. Fare clic su **Salva** per salvare e modificare le impostazioni dell'endpoint.
9. Dopo aver completato le modifiche di configurazione, fare clic su **Salva** nella parte inferiore della pagina.
10. Verificare le modifiche apportate alla configurazione come riportato di seguito:
    1.  Nella barra di ricerca del portale cercare il nome del profilo di Gestione traffico e fare clic su tale profilo nei risultati visualizzati.
    2.  Nel pannello **Profilo di Gestione traffico** fare clic su **Informazioni generali**.
    3.  Nel pannello **Profilo di Gestione traffico** viene visualizzato il nome DNS del profilo di Gestione traffico creato. Questo nome può essere usato da qualsiasi client (ad esempio raggiungendolo tramite un Web browser) per essere indirizzato all'endpoint corretto in base al tipo di routing. In questo caso, tutte le richieste vengono instradate al primo endpoint e, se Gestione traffico rileva che tale endpoint non è integro, viene eseguito automaticamente il failover del traffico sull'endpoint successivo.
11. Dopo aver verificato il funzionamento del profilo di Gestione traffico, modificare il record DNS sul server DNS autorevole per fare in modo che il nome del dominio aziendale punti al nome di dominio di Gestione traffico.

![Configurare il metodo di routing del traffico Priorità in Gestione traffico][1]

## <a name="next-steps"></a>Passaggi successivi


- Informazioni sul [metodo di routing del traffico Ponderato](traffic-manager-configure-weighted-routing-method.md).
- Informazioni sul [metodo di routing Prestazioni](traffic-manager-configure-performance-routing-method.md).
- Informazioni sul [metodo di routing Geografico](traffic-manager-configure-geographic-routing-method.md).
- Informazioni su come [testare le impostazioni di Gestione traffico](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-priority-routing-method/traffic-manager-priority-routing-method.png
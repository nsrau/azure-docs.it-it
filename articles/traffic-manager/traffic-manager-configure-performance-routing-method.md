---
title: Configurare un metodo di routing del traffico delle prestazioni con Gestione traffico di Azure | Microsoft Docs
description: "Questo articolo descrive come configurare Gestione traffico per instradare il traffico all'endpoint con latenza più bassa"
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
ms.openlocfilehash: 014aa646459cd64fca7c697419324caa3edaeeea
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="configure-the-performance-traffic-routing-method"></a>Configurare un metodo di routing del traffico delle prestazioni

Il metodo di routing del traffico Prestazioni consente di indirizzare il traffico all'endpoint con latenza più bassa dalla rete del client. In genere, il data center con latenza più bassa è quello più vicino in termini di distanza geografica. Questo metodo di routing del traffico non può basarsi per le modifiche in tempo reale sul carico o sulla configurazione di rete.

##  <a name="to-configure-performance-routing-method"></a>Per configurare un metodo di routing del traffico delle prestazioni

1. In un browser accedere al [portale di Azure](http://portal.azure.com). Se non si ha già di un account, è possibile iscriversi per ottenere una [versione di valutazione gratuita della durata di un mese](https://azure.microsoft.com/free/). 
2. Nella barra di ricerca del portale cercare i **profili di Gestione traffico** e quindi fare clic sul nome di profilo per cui si vuole configurare il metodo.
3. Nel pannello **Profilo di Gestione traffico** verificare che siano presenti sia i servizi cloud che i siti Web che si intende includere nella configurazione.
4. Nella sezione **Impostazioni** fare clic su **Configurazione** e nel pannello **Configurazione** procedere come indicato di seguito:
    1. Nelle **impostazioni del metodo di routing del traffico** per **Metodo di routing** selezionare **Prestazioni**.
    2. Specificare le stesse le stesse **impostazioni di monitoraggio degli endpoint** per tutti gli endpoint in questo profilo come indicato di seguito:
        1. Selezionare il **protocollo** appropriato e specificare il numero di **porta**. 
        2. In **Percorso** immettere una barra */*. Per monitorare gli endpoint, è necessario specificare un percorso e un nome file. Una barra ("/") è una voce valida per il percorso relativo e implica che il file si trovi nella directory radice (impostazione predefinita).
        3. Nella parte superiore della pagina fare clic su **Salva**.
5.  Verificare le modifiche apportate alla configurazione come indicato di seguito:
    1.  Nella barra di ricerca del portale cercare il nome del profilo di Gestione traffico e fare clic su tale profilo nei risultati visualizzati.
    2.  Nel pannello **Profilo di Gestione traffico** fare clic su **Informazioni generali**.
    3.  Nel pannello **Profilo di Gestione traffico** viene visualizzato il nome DNS del profilo di Gestione traffico creato. Questo nome può essere usato da qualsiasi client (ad esempio accedendovi tramite un Web browser) per essere indirizzato all'endpoint corretto in base al tipo di routing. In questo caso tutte le richieste vengono indirizzate all'endpoint con latenza più bassa dalla rete del client.
6. Dopo aver verificato il funzionamento del profilo di Gestione traffico, modificare il record DNS sul server DNS autorevole per fare in modo che il nome del dominio aziendale punti al nome di dominio di Gestione traffico.

![Configurazione del metodo di routing del traffico delle prestazioni con Gestione traffico][1]

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [metodo di routing del traffico Ponderato](traffic-manager-configure-weighted-routing-method.md).
- Informazioni sul [metodo di routing Priorità](traffic-manager-configure-priority-routing-method.md).
- Informazioni sul [metodo di routing Geografico](traffic-manager-configure-geographic-routing-method.md).
- Informazioni su come [testare le impostazioni di Gestione traffico](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-performance-routing-method/traffic-manager-performance-routing-method.png
---
title: Configurare il metodo di routing del traffico Round robin ponderato tramite Gestione traffico di Azure | Microsoft Docs
description: In questo articolo viene illustrato come bilanciare il carico del traffico con un metodo Round robin in Gestione traffico
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
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: dc2111a34fa0292a21510bbd84089979faf1cb71
ms.lasthandoff: 03/22/2017

---

# <a name="configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Configurare il metodo di routing del traffico Ponderato in Gestione traffico

Un modello comune di metodo di routing del traffico consiste nel fornire un set di endpoint identici, che includono servizi cloud e siti Web, e nell'inviare traffico a ciascuno di essi in base a uno schema round robin. I passaggi seguenti illustrano come configurare questo tipo di metodo di routing del traffico.

> [!NOTE]
> Siti Web di Azure offre già funzionalità di bilanciamento del carico round robin per i siti Web che si trovano all'interno di un data center (nota anche come area). Gestione traffico consente di specificare il metodo di routing del traffico round-robin per i siti web che si trovano in data center diversi.

## <a name="to-configure-the-weighted-traffic-routing-method"></a>Per configurare il metodo di routing del traffico Ponderato

1. Da un browser accedere al [Portale di Azure](http://portal.azure.com). Se non si ha già di un account, è possibile iscriversi per ottenere una [versione di valutazione gratuita della durata di un mese](https://azure.microsoft.com/free/). 
2. Nella barra di ricerca del portale cercare i **profili di Gestione traffico** e quindi fare clic sul nome di profilo per cui si desidera configurare il metodo.
3. Nel pannello **Profilo di Gestione traffico** verificare che siano presenti sia i servizi cloud che i siti Web da includere nella configurazione.
4. Nella sezione **Impostazioni** fare clic su **Configurazione** e nel pannello **Configurazione** procedere come riportato di seguito:
    1. In **Impostazioni del metodo di routing del traffico** verificare che il metodo di routing del traffico sia **Failover**. In caso contrario, fare clic su **Failover** nell'elenco a discesa.
    2. Definire le stesse **impostazioni di monitoraggio degli endpoint** per tutti gli endpoint in questo profilo come indicato di seguito:
        1. Selezionare il **protocollo** appropriato e specificare il numero della **porta**. 
        2. Per **Percorso**, immettere una barra rovesciata */*. Per monitorare gli endpoint, è necessario specificare un percorso e un nome file. Una barra ("/") è una voce valida per il percorso relativo e implica che il file si trovi nella directory radice (impostazione predefinita).
        3. Nella parte superiore della pagina fare clic su **Salva**.
5. Verificare le modifiche apportate alla configurazione come riportato di seguito:
    1.    Nella barra di ricerca del portale cercare il nome del profilo di Gestione traffico e fare clic su tale profilo nei risultati visualizzati.
    2.    Nel pannello **Profilo di Gestione traffico** fare clic su **Informazioni generali**.
    3.    Il pannello **Profilo di Gestione traffico** visualizza il nome DNS del profilo di Gestione traffico appena creato. Questo nome può essere usato da qualsiasi client (ad esempio raggiungendolo tramite un Web browser) che deve essere indirizzato all'endpoint corretto in base al tipo di routing. In questo caso tutte le richieste vengono instradate a ogni endpoint secondo il metodo Round robin.
6. Dopo aver verificato il funzionamento del profilo di Gestione traffico, modificare il record DNS sul server DNS autorevole per fare in modo che il nome del dominio aziendale punti al nome di dominio di Gestione traffico.

![Configurare il metodo di routing del traffico Ponderato in Gestione traffico][1]

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [metodo di routing del traffico Priorità](traffic-manager-configure-priority-routing-method.md).
- Informazioni sul [metodo di routing del traffico Prestazioni](traffic-manager-configure-performance-routing-method.md).
- Informazioni sul [metodo di routing Geografico](traffic-manager-configure-geographic-routing-method.md).
- Informazioni su come [testare le impostazioni di Gestione traffico](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-routing-method.png


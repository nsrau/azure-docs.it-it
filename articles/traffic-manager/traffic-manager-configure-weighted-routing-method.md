---
title: Configurare il routing del traffico Round Robin ponderato-gestione traffico di Azure
description: In questo articolo viene illustrato come bilanciare il carico del traffico con un metodo Round robin in Gestione traffico
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: allensu
ms.openlocfilehash: 0bfed558ec8db0ef715dad044c3965c1b1d8052b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74040341"
---
# <a name="configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Configurare il metodo di routing del traffico Ponderato in Gestione traffico

Un modello comune di metodo di routing del traffico consiste nel fornire un set di endpoint identici, che includono servizi cloud e siti Web, e nell'inviare traffico a ciascuno di essi in modo equo. I passaggi seguenti illustrano come configurare questo tipo di metodo di routing del traffico.

> [!NOTE]
> L'app Web di Azure offre già funzionalità di bilanciamento del carico round robin per i siti Web che si trovano all'interno di un'area di Azure (la quale può contenere più data center). Gestione traffico consente di distribuire il traffico tra i siti Web in diversi data center.

## <a name="to-configure-the-weighted-traffic-routing-method"></a>Per configurare il metodo di routing del traffico Ponderato

1. In un browser accedere al [portale di Azure](https://portal.azure.com). Se non si ha già di un account, è possibile iscriversi per ottenere una [versione di valutazione gratuita della durata di un mese](https://azure.microsoft.com/free/). 
2. Nella barra di ricerca del portale cercare i **profili di Gestione traffico** e quindi fare clic sul nome di profilo per cui si vuole configurare il metodo.
3. Nel pannello **Profilo di Gestione traffico** verificare che siano presenti sia i servizi cloud che i siti Web che si intende includere nella configurazione.
4. Nella sezione **Impostazioni** fare clic su **Configurazione** e nel pannello **Configurazione** procedere come riportato di seguito:
    1. In **Metodo di routing del traffico** verificare che il metodo di routing del traffico sia impostato su **Ponderato**. In caso contrario, fare clic su **Ponderato** nell'elenco a discesa.
    2. Definire le stesse **impostazioni di monitoraggio degli endpoint** per tutti gli endpoint in questo profilo come indicato di seguito:
        1. Selezionare il **protocollo** appropriato e specificare il numero di **porta**. 
        2. In **Percorso** immettere una barra */* . Per monitorare gli endpoint, è necessario specificare un percorso e un nome file. Una barra ("/") è una voce valida per il percorso relativo e implica che il file si trovi nella directory radice (impostazione predefinita).
        3. Nella parte superiore della pagina fare clic su **Salva**.
5. Verificare le modifiche apportate alla configurazione come riportato di seguito:
    1.  Nella barra di ricerca del portale cercare il nome del profilo di Gestione traffico e fare clic su tale profilo nei risultati visualizzati.
    2.  Nel pannello **Profilo di Gestione traffico** fare clic su **Informazioni generali**.
    3.  Nel pannello **Profilo di Gestione traffico** viene visualizzato il nome DNS del profilo di Gestione traffico creato. Questo nome può essere usato da qualsiasi client (ad esempio raggiungendolo tramite un Web browser) che deve essere indirizzato all'endpoint corretto in base al tipo di routing. In questo caso tutte le richieste vengono instradate a ogni endpoint secondo il metodo Round robin.
6. Dopo aver verificato il funzionamento del profilo di Gestione traffico, modificare il record DNS sul server DNS autorevole per fare in modo che il nome del dominio aziendale punti al nome di dominio di Gestione traffico.

![Configurare il metodo di routing del traffico Ponderato in Gestione traffico][1]

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [metodo di routing del traffico Priorità](traffic-manager-configure-priority-routing-method.md).
- Informazioni sul [metodo di routing del traffico Prestazioni](traffic-manager-configure-performance-routing-method.md).
- Informazioni sul [metodo di routing Geografico](traffic-manager-configure-geographic-routing-method.md).
- Informazioni su come [testare le impostazioni di Gestione traffico](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-routing-method.png

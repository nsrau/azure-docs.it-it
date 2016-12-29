---
title: Configurare i metodi di routing di Gestione traffico | Documentazione Microsoft
description: Questo articolo illustra come configurare i diversi metodi di routing in Gestione traffico
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
ms.date: 10/18/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 8827793d771a2982a3dccb5d5d1674af0cd472ce
ms.openlocfilehash: d4481115dbf8cc720019096969c55509f33d250c

---

# <a name="configure-traffic-manager-routing-methods"></a>Configurare i metodi di routing di Gestione traffico

Gestione traffico di Azure fornisce tre metodi di routing che controllano il modo in cui il traffico viene indirizzato agli endpoint del servizio disponibili. Il metodo di routing del traffico viene applicato a ogni query DNS ricevuta, per determinare l'endpoint da restituire nella risposta DNS.

In Gestione traffico sono disponibili tre metodi di routing del traffico:

* **Priorità:** selezionare "Priorità" quando si vuole usare un endpoint di servizio primario e fornire backup nel caso in cui l'endpoint primario non sia disponibile.
* **Ponderato:** selezionare "Ponderato" quando si vuole distribuire il traffico in un set di endpoint in modo uniforme o in base alle ponderazioni definite.
* **Prestazioni**: selezionare "Prestazioni" quando gli endpoint si trovano in aree geografiche diverse e si vuole che gli utenti finali usino l'endpoint "più vicino" in termini di latenza di rete più bassa.

## <a name="configure-priority-routing-method"></a>Configurare il metodo di routing Priorità

Indipendentemente dalla modalità del sito Web, i siti Web di Azure forniscono funzionalità di failover per i siti Web che si trovano in un data center (noto anche come area). Gestione traffico fornisce failover per i siti Web in diversi data center.

Un modello comune per il failover del servizio consiste nell'invio di traffico a un servizio primario e nel fornire un set di servizi di backup identici per il failover. I passaggi seguenti illustrano come configurare il failover con priorità con siti Web e servizi cloud di Azure:

1. Nel riquadro sinistro del portale di Azure classico fare clic sull'icona **Gestione traffico** per aprire il relativo riquadro.
2. Nel riquadro di Gestione traffico nel portale di Azure classico trovare il profilo di Gestione traffico contenente le impostazioni da modificare. Per aprire la pagina delle impostazioni del profilo, fare clic sulla freccia a destra del nome del profilo.
3. Nella pagina del profilo fare clic su **Endpoint** nella parte superiore della pagina. Verificare che siano presenti sia i servizi cloud che i siti Web che si vuole includere nella configurazione.
4. Fare clic su **Configura** nella parte superiore, per aprire la pagina di configurazione.
5. In **Impostazioni del metodo di routing del traffico** verificare che il metodo di routing del traffico sia **Failover**. In caso contrario, fare clic su **Failover** nell'elenco a discesa.
6. Per **Elenco priorità failover**regolare l'ordine di failover per gli endpoint. Quando si seleziona il metodo di routing del traffico **Failover** , l'ordine degli endpoint selezionati è rilevante. L'endpoint primario si trova nella parte superiore. Usare i tasti freccia SU e GIÙ per modificare l'ordine secondo le esigenze. Per informazioni su come impostare le priorità di failover usando Windows PowerShell, vedere [Set-AzureTrafficManagerProfile](http://go.microsoft.com/fwlink/p/?LinkId=400880).
7. Verificare che le **Impostazioni di monitoraggio** siano configurate correttamente. Tramite il monitoraggio viene assicurato il mancato invio di traffico agli endpoint offline. Per monitorare gli endpoint, è necessario specificare un percorso e un nome file. Una barra ("/") è una voce valida per il percorso relativo e implica che il file si trovi nella directory radice (impostazione predefinita).
8. Dopo aver completato le modifiche di configurazione, fare clic su **Salva** nella parte inferiore della pagina.
9. Verificare le modifiche apportate alla configurazione.
10. Dopo aver verificato il funzionamento del profilo di Gestione traffico, modificare il record DNS sul server DNS autorevole per fare in modo che il nome del dominio aziendale punti al nome di dominio di Gestione traffico.

## <a name="configure-weighted-routing-method"></a>Configurare il metodo di routing Ponderato

Un modello comune di metodo di routing del traffico consiste nel fornire un set di endpoint identici, che includono servizi cloud e siti Web, e nell'inviare traffico a ciascuno di essi in base a uno schema round robin. I passaggi seguenti illustrano come configurare questo tipo di metodo di routing del traffico.

> [!NOTE]
> Siti Web di Azure offre già funzionalità di bilanciamento del carico round robin per i siti Web che si trovano all'interno di un data center (nota anche come area). Gestione traffico consente di specificare il metodo di routing del traffico round-robin per i siti web che si trovano in data center diversi.

1. Nel riquadro sinistro del portale di Azure classico fare clic sull'icona **Gestione traffico** per aprire il relativo riquadro.
2. Nel riquadro di Gestione traffico trovare il profilo di Gestione traffico contenente le impostazioni da modificare. Per aprire la pagina delle impostazioni del profilo, fare clic sulla freccia a destra del nome del profilo.
3. Nella parte superiore della pagina del profilo fare clic su **Endpoint** e verificare che siano presenti gli endpoint servizio che si desidera includere nella configurazione.
4. Nella parte superiore della pagina del profilo fare clic su **Configura** per aprire la pagina di configurazione.
5. Nelle **impostazioni del metodo di routing del traffico** verificare che il metodo di routing del traffico sia **Round Robin**. In caso contrario, fare clic su **Round robin** nell'elenco a discesa.
6. Verificare che le **Impostazioni di monitoraggio** siano configurate correttamente. Tramite il monitoraggio viene assicurato il mancato invio di traffico agli endpoint offline. Per monitorare gli endpoint, è necessario specificare un percorso e un nome file. Una barra ("/") è una voce valida per il percorso relativo e implica che il file si trovi nella directory radice (impostazione predefinita).
7. Dopo aver completato le modifiche di configurazione, fare clic su **Salva** nella parte inferiore della pagina.
8. Verificare le modifiche apportate alla configurazione.
9. Dopo aver verificato il funzionamento del profilo di Gestione traffico, modificare il record DNS sul server DNS autorevole per fare in modo che il nome del dominio aziendale punti al nome di dominio di Gestione traffico.

## <a name="configure-performance-traffic-routing-method"></a>Configurare un metodo di routing del traffico delle prestazioni

Il metodo di routing del traffico Prestazioni consente di indirizzare il traffico all'endpoint con latenza più bassa dalla rete del client. In genere, il data center con latenza più bassa è quello più vicino in termini di distanza geografica. Questo metodo di routing del traffico non può basarsi per le modifiche in tempo reale sul carico o sulla configurazione di rete.

1. Nel riquadro sinistro del portale di Azure classico fare clic sull'icona **Gestione traffico** per aprire il relativo riquadro.
2. Nel riquadro di Gestione traffico trovare il profilo di Gestione traffico contenente le impostazioni da modificare. Per aprire la pagina delle impostazioni del profilo, fare clic sulla freccia a destra del nome del profilo.
3. Nella parte superiore della pagina del profilo fare clic su **Endpoint** e verificare che siano presenti gli endpoint servizio che si desidera includere nella configurazione.
4. Nella parte superiore della pagina del profilo fare clic su **Configura** per aprire la pagina di configurazione.
5. Nelle **impostazioni del metodo di routing del traffico** verificare che il metodo di routing del traffico sia **Prestazioni**. In caso contrario, fare clic su **Prestazioni** nell'elenco a discesa.
6. Verificare che le **Impostazioni di monitoraggio** siano configurate correttamente. Tramite il monitoraggio viene assicurato il mancato invio di traffico agli endpoint offline. Per monitorare gli endpoint, è necessario specificare un percorso e un nome file. Una barra ("/") è una voce valida per il percorso relativo e implica che il file si trovi nella directory radice (impostazione predefinita).
7. Dopo aver completato le modifiche di configurazione, fare clic su **Salva** nella parte inferiore della pagina.
8. Verificare le modifiche apportate alla configurazione.
9. Dopo aver verificato il funzionamento del profilo di Gestione traffico, modificare il record DNS sul server DNS autorevole per fare in modo che il nome del dominio aziendale punti al nome di dominio di Gestione traffico.

## <a name="next-steps"></a>Passaggi successivi

* [Gestire i profili di Gestione traffico](traffic-manager-manage-profiles.md)
* [Metodi di routing di Gestione traffico](traffic-manager-routing-methods.md)
* [Verifica delle impostazioni di Gestione traffico](traffic-manager-testing-settings.md)
* [Impostare un dominio Internet aziendale in modo che punti a un dominio di Gestione traffico](traffic-manager-point-internet-domain.md)
* [Manage Traffic Manager endpoints](traffic-manager-manage-endpoints.md) (Gestire gli endpoint di Gestione traffico)
* [Risoluzione dei problemi relativi allo stato Danneggiato di Gestione traffico](traffic-manager-troubleshooting-degraded.md)




<!--HONumber=Nov16_HO5-->



---
title: Gestire i profili di Gestione Traffico di Azure | Documentazione Microsoft
description: Questo articolo fornisce istruzioni per creare, disabilitare, abilitare, eliminare e visualizzare la cronologia di un profilo di Gestione traffico di Azure.
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: f06e0365-0a20-4d08-b7e1-e56025e64f66
ms.service: traffic-manager
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 8827793d771a2982a3dccb5d5d1674af0cd472ce
ms.openlocfilehash: 7e7de7dc1eca6903403afef03fdd6afb98ff16c9

---

# <a name="manage-an-azure-traffic-manager-profile"></a>Gestire un profilo di Gestione traffico di Azure

I profili di Gestione traffico di Azure usano metodi di routing del traffico per controllare la distribuzione del traffico verso i servizi cloud o gli endpoint di siti Web. Questo articolo illustra come creare e gestire tali profili.

## <a name="create-a-traffic-manager-profile-using-quick-create"></a>Creare un profilo di Gestione traffico mediante Creazione rapida

È possibile creare rapidamente un profilo di Gestione traffico usando Creazione rapida nel portale di Azure classico. Creazione rapida consente di creare profili con impostazioni di configurazione di base. Tuttavia, non è possibile utilizzare Creazione rapida per impostazioni come il set di endpoint (servizi cloud e siti Web), l'ordine di failover per il relativo metodo di routing del traffico o per le impostazioni di monitoraggio. Dopo aver creato il proprio profilo è possibile configurare queste impostazioni nel portale di Azure classico. Gestione traffico supporta fino a 200 endpoint per profilo. Tuttavia, la maggior parte degli scenari di utilizzo richiede solo un numero ridotto di endpoint.

### <a name="to-create-a-traffic-manager-profile"></a>Per creare un profilo di Gestione traffico

1. **Distribuire i servizi cloud e i siti Web nell'ambiente di produzione.** Per altre informazioni sui servizi cloud, vedere [Servizi cloud](http://go.microsoft.com/fwlink/p/?LinkId=314074). Per altre informazioni sui siti Web, vedere [Siti Web](http://go.microsoft.com/fwlink/p/?LinkId=393327).
2. **Accedere al portale di Azure classico.** Fare clic su **Nuovo** in basso a sinistra nel portale, fare clic su **Servizi di rete > Gestione traffico**, scegliere **Creazione rapida** per avviare la configurazione del profilo.
3. **Configurare il prefisso DNS.**  Attribuire al profilo di Gestione traffico un nome del prefisso DNS univoco. È possibile specificare solo il prefisso per un nome di dominio di Gestione traffico.
4. **Selezionare la sottoscrizione.**  Selezionare la sottoscrizione di Azure appropriata. Ogni profilo è associato a una singola sottoscrizione. Se è disponibile solo una sottoscrizione, questa opzione non è visibile.
5. **Selezionare il metodo di routing del traffico.** Selezionare il metodo di routing del traffico in **criteri di routing del traffico**. Per altre informazioni sui metodi di routing del traffico, vedere [Informazioni sui metodi di routing del traffico di Gestione traffico](traffic-manager-routing-methods.md).
6. **Fare clic su "Crea" per creare il profilo**. Dopo il completamento della configurazione del profilo, è possibile trovare il proprio profilo nel riquadro Gestione traffico nel portale di Azure classico.
7. **Configurare endpoint, monitoraggio e impostazioni aggiuntive nel portale di Azure classico.** Creazione rapida consente di configurare solo le impostazioni di base. È necessario configurare impostazioni aggiuntive, ad esempio l'elenco di endpoint e l'ordine di failover degli endpoint.

## <a name="disable-enable-or-delete-a-profile"></a>Disabilitare, abilitare o eliminare un profilo

È possibile disabilitare un profilo di Gestione traffico esistente in modo che non indirizzi le richieste dell'utente agli endpoint configurati. Quando si disabilita un profilo di Gestione traffico, il profilo stesso e le informazioni in esso contenute rimangono invariati e possono essere modificati nell'interfaccia di Gestione traffico.  I riferimenti vengono ripristinati quando si riabilita il profilo. Un profilo di Gestione traffico creato nel portale di Azure classico viene abilitato automaticamente. Se si decide che un profilo non sarà più necessario, è possibile eliminarlo.

### <a name="to-disable-a-profile"></a>Per disabilitare un profilo

1. Se si usa un nome di dominio personalizzato, modificare il record CNAME per il server DNS Internet in modo che non punti più al profilo di Gestione traffico.
2. Il traffico non sarà più indirizzato agli endpoint attraverso le impostazioni del profilo di Gestione traffico.
3. Selezionare il profilo da disabilitare. Nella pagina di Gestione traffico evidenziare il profilo facendo clic sulla colonna accanto al nome del profilo. Notare che facendo clic sul nome del profilo o sulla freccia accanto al nome, si aprirà la pagina delle impostazioni del profilo.
4. Dopo aver selezionato il profilo, fare clic su **Disabilita** nella parte inferiore della pagina.

### <a name="to-enable-a-profile"></a>Per abilitare un profilo

1. Selezionare il profilo da disabilitare. Nella pagina di Gestione traffico evidenziare il profilo facendo clic sulla colonna accanto al nome del profilo. Notare che facendo clic sul nome del profilo o sulla freccia accanto al nome, si aprirà la pagina delle impostazioni del profilo.
2. Dopo aver selezionato il profilo, fare clic su **Abilita** nella parte inferiore della pagina.
3. Se si usa un nome di dominio personalizzato, creare un record di risorse CNAME sul server DNS Internet in modo da puntare al nome di dominio del profilo di Gestione traffico.
4. Il traffico verrà nuovamente indirizzato verso gli endpoint.

### <a name="to-delete-a-profile"></a>Per eliminare un profilo

1. Assicurarsi che il record di risorse DNS sul server DNS Internet non usi più un record di risorse CNAME che punta al nome di dominio del profilo di Gestione traffico.
2. Selezionare il profilo da disabilitare. Nella pagina di Gestione traffico evidenziare il profilo facendo clic sulla colonna accanto al nome del profilo. Notare che facendo clic sul nome del profilo o sulla freccia accanto al nome, si aprirà la pagina delle impostazioni del profilo.
3. Dopo aver selezionato il profilo, fare clic su **Elimina** nella parte inferiore della pagina.

## <a name="view-traffic-manager-profile-change-history"></a>Visualizzare la cronologia delle modifiche del profilo Gestione traffico

È possibile visualizzare la cronologia delle modifiche per il profilo di Gestione traffico in Servizi di gestione nel portale di Azure classico.

### <a name="to-view-your-traffic-manager-change-history"></a>Per visualizzare la cronologia delle modifiche di Gestione traffico

1. Nel riquadro sinistro del portale di Azure classico fare clic su **Servizi di gestione**.
2. Nella pagina Servizi di gestione fare clic su **Registri operazioni**.
3. Nella pagina Registri operazioni è possibile applicare un filtro per visualizzare la cronologia delle modifiche per il profilo di Gestione traffico. Dopo aver selezionato le opzioni di filtro, fare clic sul segno di spunta per visualizzare i risultati.

   * Per visualizzare le modifiche per tutti i profili, selezionare la sottoscrizione, l'intervallo di tempo e selezionare **Gestione traffico** dall'elenco a discesa **Tipo**.
   * Per applicare un filtro in base al nome del profilo, digitare il nome del profilo nel campo **Nome servizio** oppure selezionarlo dall'elenco a discesa.
   * Per visualizzare i dettagli di ogni singola modifica, selezionare la riga con la modifica che si vuole visualizzare, quindi fare clic su **Dettagli** nella parte inferiore della pagina. Nella finestra **Dettagli operazione** è possibile visualizzare la rappresentazione XML dell'oggetto API creato o aggiornato come parte dell'operazione.

## <a name="next-steps"></a>Passaggi successivi

* [Aggiungere un endpoint](traffic-manager-endpoints.md)
* [Configurare metodo di routing failover](traffic-manager-configure-failover-routing-method.md)
* [Configurare il metodo di routing Round Robin](traffic-manager-configure-round-robin-routing-method.md)
* [Configurare un metodo di routing del traffico delle prestazioni](traffic-manager-configure-performance-routing-method.md)
* [Impostare un dominio Internet aziendale in modo che punti a un dominio di Gestione traffico](traffic-manager-point-internet-domain.md)
* [Risoluzione dei problemi relativi allo stato Danneggiato di Gestione traffico](traffic-manager-troubleshooting-degraded.md)



<!--HONumber=Nov16_HO5-->



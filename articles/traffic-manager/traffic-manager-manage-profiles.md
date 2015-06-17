<properties
   pageTitle="Gestione profili di Gestione traffico"
   description="In questo articolo vengono fornite istruzioni per creare, disattivare, attivare, eliminare e visualizzare la cronologia di un profilo di Gestione traffico".
   services="traffic-manager"
   documentationCenter=""
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/27/2015"
   ms.author="cherylmc" />

# Gestione di un profilo di Gestione traffico

Per specificare gli endpoint dei servizi cloud o dei siti Web da monitorare tramite Gestione traffico e il metodo di bilanciamento del carico da utilizzare per distribuire le connessioni con tali endpoint, viene utilizzato un profilo di Gestione traffico. 

## Creazione di un profilo di Gestione traffico tramite Creazione rapida

È possibile creare rapidamente un profilo di Gestione traffico utilizzando **Creazione rapida** nel portale di gestione. Creazione rapida consente di creare profili con impostazioni di configurazione di base. Tuttavia, non è possibile utilizzare Creazione rapida per impostazioni come il set di endpoint (servizi cloud e siti Web), l'ordine di failover per il relativo metodo di bilanciamento del carico o per le impostazioni di monitoraggio. Dopo aver creato il proprio profilo è possibile configurare tali impostazioni nel portale di gestione. Per un elenco delle procedure di configurazione, vedere [Attività di configurazione di Gestione traffico](https://msdn.microsoft.com/library/azure/hh744830.aspx).

### Per creare un nuovo profilo di Gestione traffico

1. **Distribuzione dei servizi cloud e dei siti Web nel proprio ambiente di produzione.** Per altre informazioni sui servizi cloud, vedere [Servizi cloud](http://go.microsoft.com/fwlink/p/?LinkId=314074). Per informazioni sui servizi cloud, vedere [Procedure consigliate](https://msdn.microsoft.com/library/azure/5229dd1c-5a91-4869-8522-bed8597d9cf5#bkmk_TrafficManagerBestPracticesProfile). Per altre informazioni sui siti Web, vedere [Siti Web](http://go.microsoft.com/fwlink/p/?LinkId=393327).

2. **Accesso al portale di gestione.** Per creare un nuovo profilo di Gestione traffico, fare clic su **Nuovo** in basso a sinistra sul portale, fare clic su **Servizi di rete**, selezionare **Gestione traffico**, quindi scegliere **Creazione rapida** per avviare la configurazione del profilo.
3. **Configurazione del prefisso DNS.** Fornire un nome di prefisso DNS univoco al profilo di Gestione traffico. Per il nome del dominio di Gestione traffico, è possibile specificare solo il prefisso.
4. **Selezione della sottoscrizione.** Selezionare la sottoscrizione di Azure appropriata. Ciascun profilo è associato a una singola sottoscrizione. Se si dispone di una sola sottoscrizione, quest'opzione non viene visualizzata.
5. **Selezione del metodo di bilanciamento del carico.** In **Criterio di bilanciamento del carico**, selezionare il metodo di bilanciamento del carico. Per altre informazioni sui metodi di bilanciamento del carico, vedere [Informazioni sui metodi di bilanciamento del carico di Gestione Traffico](traffic-manager-load-balancing-methods.md).
6. **Fare clic su "Crea" per creare il nuovo profilo**. Dopo il completamento della configurazione del profilo, è possibile individuare il proprio profilo nel riquadro Gestione traffico nel portale di gestione.
7. **Configurazione endpoint, monitoraggio e impostazioni aggiuntive nel portale di gestione.** Utilizzando Creazione rapida è possibile configurare solo le impostazioni di base. Quindi, per poter completare la configurazione desiderata, è necessario configurare delle impostazioni aggiuntive come l'elenco degli endpoint e l'ordine di failover dell'endpoint. Per un elenco delle procedure per eseguire il completamento della configurazione, vedere [Attività di configurazione di Gestione traffico](https://msdn.microsoft.com/library/azure/hh744830.aspx).

## Disabilitazione, abilitazione o eliminazione di un profilo

È possibile disabilitare un profilo di Gestione traffico esistente in modo che non possa fare riferimento alle richieste dell'utente per gli endpoint configurati. Quando si disabilita un profilo di Gestione traffico, quest'ultimo e le informazioni in esso contenute rimarranno intatte e potranno essere modificate nell'interfaccia di Gestione traffico. Quando si desidera abilitare nuovamente il profilo, è possibile effettuare l'operazione nel portale di gestione e i riferimenti verranno ripresi. Un profilo di Gestione traffico si abilita automaticamente quando viene creato nel portale di gestione. Se si decide che un profilo non sarà più necessario, è possibile eliminarlo. 

### Per disabilitare un profilo

1. Modificare il record di risorse DNS sul server DNS Internet per utilizzare il tipo di record appropriato e il puntatore per un altro nome o l'indirizzo IP di un percorso specifico su Internet. In altre parole, modificare il record di risorse DNS sul server DNS Internet per non utilizzare più un record di risorse CNAME che fa riferimento al nome di dominio del profilo di Gestione traffico.
2. Il traffico non verrà più indirizzato agli endpoint tramite le impostazioni del profilo di Gestione traffico.
3. Selezionare il profilo che si desidera disabilitare. Per selezionare il profilo, evidenziarlo sulla pagina di Gestione traffico selezionando la colonna accanto al nome del profilo. Non selezionare il nome del profilo o la freccia accanto al nome. Questa operazione apre la relativa pagina delle impostazioni.
4. Dopo aver selezionato il profilo, fare clic su **Disabilita** nella parte inferiore della pagina.

### Per abilitare un profilo

1. Selezionare il profilo che si desidera abilitare. Per selezionare il profilo, evidenziarlo sulla pagina di Gestione traffico selezionando la colonna accanto al nome del profilo. Non selezionare il nome del profilo o la freccia accanto al nome. Questa operazione apre la relativa pagina delle impostazioni.
2. Dopo aver selezionato il profilo, fare clic su **Abilita** nella parte inferiore della pagina.
3. Modificare il record di risorse DNS sul server DNS Internet per utilizzare il tipo di record CNAME. Tale record esegue il mapping del nome di dominio aziendale sul nome di dominio del profilo di Gestione traffico. Per altre informazioni, vedere [Scegliere un dominio Internet aziendale per un dominio di Gestione traffico](traffic-manager-point-internet-domain.md).
4. Il traffico verrà indirizzato nuovamente agli endpoint.

### Per eliminare un profilo

1. Assicurarsi che il record di risorse DNS sul server DNS Internet non usi più un record di risorse CNAME che fa riferimento al nome di dominio del profilo di Gestione traffico.
2. Selezionare il profilo che si desidera eliminare. Per selezionare il profilo, evidenziarlo sulla pagina di Gestione traffico selezionando la colonna accanto al profilo. Non selezionare il nome del profilo o la freccia accanto al nome. Questa operazione apre la relativa pagina delle impostazioni.
4. Dopo aver selezionato il profilo, fare clic su **Elimina** nella parte inferiore della pagina.

## Visualizzare la cronologia delle modifiche del profilo di Gestione traffico

È possibile visualizzare la cronologia delle modifiche per il profilo di Gestione traffico nel portale di gestione dei Servizi di gestione.

### Per visualizzare la cronologia delle modifiche di Gestione traffico

1. Nel riquadro sinistro del portale di gestione fare clic su **Servizi di gestione**.
2. Nella pagina Servizi di gestione fare clic su **Registri operazioni**.
3. Nella pagina Registri operazioni è possibile applicare un filtro per visualizzare la cronologia delle modifiche per il profilo di Gestione traffico. Dopo aver selezionato le opzioni di filtro, fare clic sul segno di spunta per visualizzare i risultati.
   - Per visualizzare le modifiche del profilo per tutti i profili, selezionare la sottoscrizione, l'intervallo di tempo e selezionare **Gestione traffico** dall'elenco a discesa **Tipo**.
   - Per applicare un filtro in base al nome del profilo, digitare il nome del profilo nel campo **Nome servizio** oppure selezionarlo dall'elenco a discesa.
   - Per visualizzare i dettagli di ogni singola modifica, selezionare la riga con la modifica che si vuole visualizzare, quindi fare clic su **Dettagli** nella parte inferiore della pagina. Nella finestra **Dettagli operazione**, è possibile visualizzare la rappresentazione XML dell'oggetto API creato o aggiornato come parte dell'operazione e copiare il codice XML negli Appunti.


## Vedere anche

[Panoramica di Gestione traffico](traffic-manager-overview.md)

[Gestione endpoint in Gestione traffico](traffic-manager-endpoints.md)

[Informazioni sul monitoraggio di Gestione traffico](traffic-manager-monitoring.md)

[Attività di configurazione di Gestione traffico](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[Operazioni relative a Gestione traffico (riferimento all'API REST)](http://go.microsoft.com/fwlink/p/?LinkID=313584)

[Servizi cloud](http://go.microsoft.com/fwlink/?LinkId=314074)

[Siti Web](http://go.microsoft.com/fwlink/p/?LinkId=393327)

<!--HONumber=49--> 
<properties
   pageTitle="Gestire i profili di Gestione Traffico di Azure | Microsoft Azure"
   description="In questo articolo vengono fornite istruzioni per creare, disattivare, attivare, eliminare e visualizzare la cronologia di un profilo di Gestione traffico di Azure"
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/19/2015"
   ms.author="joaoma" />

# Gestione profili di Gestione traffico di Azure

Per specificare gli endpoint dei servizi cloud o dei siti Web da monitorare tramite Gestione traffico e il metodo di routing del traffico da utilizzare per distribuire le connessioni con tali endpoint, viene utilizzato un profilo di Gestione traffico.

## Creare un profilo di Gestione traffico mediante Creazione rapida

È possibile creare rapidamente un profilo di Gestione traffico usando Creazione rapida nel portale di Azure. Creazione rapida consente di creare profili con impostazioni di configurazione di base. Tuttavia, non è possibile utilizzare Creazione rapida per impostazioni come il set di endpoint (servizi cloud e siti Web), l'ordine di failover per il relativo metodo di routing del traffico o per le impostazioni di monitoraggio. Dopo aver creato il proprio profilo è possibile configurare tali impostazioni nel portale di Azure. Gestione traffico supporta fino a 200 endpoint per profilo. Tuttavia, la maggior parte degli scenari di utilizzo richiede solo un numero ridotto di endpoint.

### Per creare un nuovo profilo di Gestione traffico

1. **Distribuire i servizi cloud e i siti Web nell'ambiente di produzione.** Per altre informazioni sui servizi cloud, vedere [Servizi cloud](http://go.microsoft.com/fwlink/p/?LinkId=314074). Per informazioni sui servizi cloud, vedere [Procedure consigliate](https://msdn.microsoft.com/library/azure/5229dd1c-5a91-4869-8522-bed8597d9cf5#bkmk_TrafficManagerBestPracticesProfile). Per altre informazioni sui siti Web, vedere [Siti Web](http://go.microsoft.com/fwlink/p/?LinkId=393327).

2. **Accedere al portale di Azure.** Per creare un nuovo profilo di Gestione traffico, fare clic su **Nuovo** in basso a sinistra nel portale, fare clic su **Servizi di rete > Gestione traffico**, scegliere **Creazione rapida** per avviare la configurazione del profilo.
3. **Configurare il prefisso DNS.** Attribuire al profilo di Gestione traffico un nome del prefisso DNS univoco. È possibile specificare solo il prefisso per un nome di dominio di Gestione traffico.
4. **Selezionare la sottoscrizione.** Selezionare la sottoscrizione di Azure appropriata. Ogni profilo è associato a una singola sottoscrizione. Se è disponibile solo una sottoscrizione, questa opzione non è visibile.
5. **Selezionare il metodo di routing del traffico.** Selezionare il metodo di routing del traffico in **criteri di routing del traffico**. Per altre informazioni sui metodi di routing del traffico, vedere [Informazioni sui metodi di routing del traffico di Gestione traffico](traffic-manager-load-balancing-methods.md).
6. **Fare clic su "Crea" per creare il nuovo profilo.** Dopo il completamento della configurazione del profilo, è possibile individuare il proprio profilo nel riquadro Gestione traffico nel portale di Azure.
7. **Configurare endpoint, monitoraggio e impostazioni aggiuntive nel portale di Azure.** Poiché con Creazione rapida è possibile configurare solo impostazioni di base, per completare la configurazione desiderata, è necessario configurare impostazioni aggiuntive, ad esempio l'elenco degli endpoint e l'ordine di failover dell'endpoint. 


## Disabilitare, abilitare o eliminare un profilo

È possibile disabilitare un profilo di Gestione traffico esistente in modo che non indirizzi le richieste dell'utente agli endpoint configurati. Quando si disabilita un profilo di Gestione traffico, il profilo stesso e le informazioni in esso contenute rimarranno invariati e potranno essere modificati nell'interfaccia di Gestione traffico. Quando si desidera abilitare nuovamente il profilo, è possibile effettuare l'operazione nel portale di Azure e i riferimenti verranno ripresi. Un profilo di Gestione traffico creato nel portale di Azure viene abilitato automaticamente. Se si decide che un profilo non sarà più necessario, è possibile eliminarlo.

### Per disabilitare un profilo

1. Modificare il record di risorse DNS sul server DNS Internet in modo che usi il tipo di record e il puntatore appropriati, immettendo un altro nome o l'indirizzo IP di un percorso specifico su Internet. In altre parole, modificare il record di risorse DNS sul server DNS Internet in modo che non usi più un record di risorse CNAME che punta al nome di dominio del profilo di Gestione traffico.
2. L'indirizzamento del traffico agli endpoint verrà arrestato tramite le impostazioni del profilo di Gestione traffico.
3. Selezionare il profilo da disabilitare. Per selezionare il profilo, nella pagina di Gestione traffico evidenziare il profilo facendo clic sulla colonna accanto al nome del profilo. Non fare clic sul nome del profilo o sulla freccia accanto al nome, altrimenti verrà visualizzata la pagina delle impostazioni del profilo.
4. Dopo aver selezionato il profilo, fare clic su **Disabilita** nella parte inferiore della pagina.

### Per abilitare un profilo

1. Selezionare il profilo da abilitare. Per selezionare il profilo, nella pagina di Gestione traffico evidenziare il profilo facendo clic sulla colonna accanto al nome del profilo. Non fare clic sul nome del profilo o sulla freccia accanto al nome, altrimenti verrà visualizzata la pagina delle impostazioni del profilo.
2. Dopo aver selezionato il profilo, fare clic su **Abilita** nella parte inferiore della pagina.
3. Modificare il record di risorse DNS sul server DNS Internet in modo che usi il tipo di record CNAME, che esegue il mapping del nome di dominio aziendale al nome di dominio del profilo di Gestione traffico. Per altre informazioni, vedere [Impostare un dominio Internet aziendale in modo che punti a un dominio di Gestione traffico](traffic-manager-point-internet-domain.md).
4. Il traffico verrà nuovamente indirizzato verso gli endpoint.

### Per eliminare un profilo

1. Assicurarsi che il record di risorse DNS sul server DNS Internet non usi più un record di risorse CNAME che punta al nome di dominio del profilo di Gestione traffico.
2. Selezionare il profilo da eliminare. Per selezionare il profilo, evidenziarlo sulla pagina di Gestione traffico selezionando la colonna accanto al profilo. Non fare clic sul nome del profilo o sulla freccia accanto al nome, altrimenti verrà visualizzata la pagina delle impostazioni del profilo.
4. Dopo aver selezionato il profilo, fare clic su **Elimina** nella parte inferiore della pagina.

## Visualizzare la cronologia delle modifiche del profilo Gestione traffico

È possibile visualizzare la cronologia delle modifiche per il profilo di Gestione traffico nel portale di Azure in Servizi di gestione.

### Per visualizzare la cronologia delle modifiche di Gestione traffico

1. Nel riquadro sinistro del portale di Azure fare clic su **Servizi di gestione**.
2. Nella pagina Servizi di gestione fare clic su **Registri operazioni**.
3. Nella pagina Registri operazioni è possibile applicare un filtro per visualizzare la cronologia delle modifiche per il profilo di Gestione traffico. Dopo aver selezionato le opzioni di filtro, fare clic sul segno di spunta per visualizzare i risultati.
   - Per visualizzare le modifiche del profilo per tutti i profili, selezionare la sottoscrizione, l'intervallo di tempo e selezionare **Gestione traffico** dall'elenco a discesa **Tipo**.
   - Per applicare un filtro in base al nome del profilo, digitare il nome del profilo nel campo **Nome servizio** oppure selezionarlo dall'elenco a discesa.
   - Per visualizzare i dettagli di ogni singola modifica, selezionare la riga con la modifica che si vuole visualizzare, quindi fare clic su **Dettagli** nella parte inferiore della pagina. Nella finestra **Dettagli operazione**, è possibile visualizzare la rappresentazione XML dell'oggetto API creato o aggiornato come parte dell'operazione e copiare il codice XML negli Appunti.


## Risorse aggiuntive

[Gestione traffico di Azure](traffic-manager-overview.md)

[Gestione endpoint in Gestione traffico](traffic-manager-endpoints.md)

[Informazioni sul monitoraggio di Gestione traffico](traffic-manager-monitoring.md)

[Gestione traffico: disabilitare, abilitare o eliminare un profilo](disable-enable-or-delete-a-profile.md)

[Gestione traffico: disabilitare o abilitare un endpoint](disable-or-enable-an-endpoint.md)

[Operazioni per Gestione traffico (informazioni di riferimento API REST)](http://go.microsoft.com/fwlink/p/?LinkID=313584)

[Servizi cloud](http://go.microsoft.com/fwlink/?LinkId=314074)

[Siti Web](http://go.microsoft.com/fwlink/p/?LinkId=393327)

<!---HONumber=August15_HO8-->
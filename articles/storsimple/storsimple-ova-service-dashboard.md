<properties 
   pageTitle="Dashboard del servizio StorSimple Manager: array virtuale | Microsoft Azure"
   description="Descrive il dashboard del servizio StorSimple Manager e illustra come usarlo per monitorare l'integrità dell'array virtuale StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/07/2016"
   ms.author="alkohli" />

# Usare il dashboard del servizio StorSimple Manager per l'array virtuale StorSimple

## Panoramica

La pagina dashboard del servizio StorSimple Manager fornisce una visualizzazione di riepilogo degli array virtuali StorSimple (noti anche come dispositivi virtuali locali StorSimple o dispositivi virtuali) connessi al servizio StorSimple Manager, evidenziando quelli che richiedono attenzione da parte dell'amministratore di sistema. Questa esercitazione introduce la pagina dashboard, illustra il contenuto e la funzione del dashboard e descrive le attività che si possono eseguire da questa pagina.

![Dashboard del servizio](./media/storsimple-ova-service-dashboard/dashboard1.png)

Nel dashboard del servizio StorSimple Manager sono visualizzate le informazioni seguenti:

- Nell'area del **grafico**, nella parte superiore della pagina, è possibili visualizzare le metriche rilevanti per i dispositivi virtuali. È possibile visualizzare l'archiviazione primaria usata in tutti i dispositivi virtuali, nonché l'archiviazione cloud utilizzata dai dispositivi virtuali per un periodo di tempo. Usare i controlli nell'angolo in alto a destra del grafico per specificare l'utilizzo relativo o assoluto e per visualizzare una scala temporale di 1 settimana, 1 mese, 3 mesi o 1 anno. Usare il controllo di aggiornamento ![controllo di aggiornamento](./media/storsimple-ova-service-dashboard/refresh-control.png) per aggiornare il grafico.

- La **panoramica sull'utilizzo** illustra l'archiviazione primaria fornita e utilizzata da tutti i dispositivi virtuali rispetto all'archiviazione totale disponibile tra tutti i dispositivi virtuali. **Fornito** si riferisce alla quantità di spazio di archiviazione preparata e allocata per l'uso, **utilizzato** si riferisce all'uso delle condivisioni o dei volumi come visualizzato dagli iniziatori connessi ai dispositivi virtuali e **capacità massima** mostra la capacità massima di tutti i dispositivi virtuali.

- L’area **avvisi** fornisce uno snapshot di tutti gli avvisi attivi per tutti i dispositivi virtuali, raggruppati in base alla gravità. Cliccando sul livello di gravità si apre la pagina **avvisi**, la quale è stata programmata per mostrare gli avvisi. Sulla pagina **avvisi**, è possibile fare clic su un singolo avviso per visualizzare ulteriori dettagli su tale avviso, incluse tutte le operazioni consigliate. È inoltre possibile cancellare l'avviso se il problema è stato risolto.

- L’area **processi** fornisce uno snapshot dei processi recenti in tutti i dispositivi virtuali connessi al servizio. Sono disponibili dei collegamenti che possono essere utilizzati per esaminare i processi attualmente in corso e quelli che hanno avuto esito positivo o negativo nelle ultime 24 ore.

- L’area di **riepilogo rapido** disponibile nella parte destra della pagina fornisce informazioni utili, ad esempio lo stato del servizio, il numero totale di dispositivi virtuali connessi al servizio, il percorso del servizio e i dettagli della sottoscrizione associata al servizio. Viene inoltre fornito un collegamento al log delle operazioni. Fare clic sul collegamento per visualizzare un elenco di tutte le operazioni completate del servizio StorSimple Manager.

È possibile usare il servizio StorSimple Manager per eseguire le attività seguenti:

- Ottenere la chiave di registrazione del servizio.
- Visualizzare i log delle operazioni.

## Ottenere la chiave di registrazione del servizio

Il codice di registrazione del servizio viene usato per registrare un dispositivo virtuale StorSimple con il servizio StorSimple Manager, in modo che il dispositivo venga visualizzato nel portale di Azure classico per altre azioni di gestione. La chiave viene creata sul primo dispositivo virtuale ed è condivisa con i rimanenti dispositivi virtuali.

Cliccando su **codice di registrazione** (nella parte inferiore della pagina) consente di aprire la finestra di dialogo **codice di registrazione del servizio**, dalla quale è possibile copiare la chiave di registrazione del servizio corrente negli Appunti o rigenerare il codice di registrazione del servizio.

![chiave di registrazione](./media/storsimple-ova-service-dashboard/service-dashboard3.png)

La rigenerazione della chiave non influisce sui dispositivi virtuali registrati in precedenza: sarà valida solo per i dispositivi virtuali registrati con il servizio dopo la rigenerazione della chiave.

Per altre informazioni sulla chiave di registrazione, vedere il video su [come ottenere la chiave di registrazione del servizio](storsimple-ova-manage-service.md#get-the-service-registration-key).

## Visualizzare i log delle operazioni

È possibile visualizzare il log delle operazioni facendo clic sul collegamento dei log delle operazione disponibile nel riquadro **riepilogo rapido** del dashboard. Verrà visualizzata la pagina Servizi di gestione, dove è possibile filtrare e visualizzare i registri specifici per il servizio StorSimple Manager.

![Log delle operazioni](./media/storsimple-ova-service-dashboard/ops-log.png)

## Passaggi successivi

Scoprire come usare l'interfaccia utente Web locale per [amministrare l'array virtuale StorSimple](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0413_2016-->
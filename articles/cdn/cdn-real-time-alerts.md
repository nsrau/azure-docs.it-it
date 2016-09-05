<properties
	pageTitle="Avvisi in tempo reale nella rete CDN di Microsoft Azure | Microsoft Azure"
	description="Avvisi in tempo reale nella rete CDN di Microsoft Azure. Gli avvisi in tempo reale forniscono notifiche sulle prestazioni degli endpoint nel profilo della rete CDN."
	services="cdn"
	documentationCenter=""
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/12/2016"
	ms.author="casoper"/>  

# Avvisi in tempo reale nella rete CDN di Microsoft Azure

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]


## Overview

Questo documento illustra gli avvisi in tempo reale nella rete CDN di Microsoft Azure. Questa funzionalità fornisce notifiche in tempo reale sulle prestazioni degli endpoint nel profilo della rete CDN. È possibile configurare avvisi HTTP o di posta elettronica in base a quanto segue:

* Larghezza di banda
* Codici di stato
* Stati della cache
* Connessioni

## Creazione di un avviso in tempo reale

1. Nel [portale di Azure](https://portal.azure.com) passare al profilo della rete CDN.

	![Pannello del profilo di rete CDN](./media/cdn-real-time-alerts/cdn-profile-blade.png)  

2. Nel pannello relativo al profilo della rete CDN fare clic sul pulsante **Gestisci**.

	![Pulsante Gestisci pannello del profilo di rete CDN](./media/cdn-real-time-alerts/cdn-manage-btn.png)  

	Si aprirà il portale di gestione della rete CDN.

3. Passare il puntatore sulla scheda **Analisi**, quindi sul riquadro a comparsa **Statistiche in tempo reale**. Fare clic su **Real-Time Alerts** (Avvisi in tempo reale).

	![Portale di gestione della rete CDN](./media/cdn-real-time-alerts/cdn-premium-portal.png)  

	Verrà visualizzato l'elenco delle configurazioni di avviso esistenti (se presenti).

4. Fare clic sul pulsante **Add Alert** (Aggiungi avviso).

	![Pulsante Add Alert (Aggiungi avviso)](./media/cdn-real-time-alerts/cdn-add-alert.png)  

	Verrà visualizzato un form per la creazione di un nuovo avviso.

	![Form New Alert (Nuovo avviso)](./media/cdn-real-time-alerts/cdn-new-alert.png)  

5. Per attivare l'avviso quando si fa clic su **Save** (Salva), selezionare la casella di controllo **Alert Enabled** (Avviso abilitato).

6. Immettere un nome descrittivo nel campo **Name** (Nome).

7. Nell'elenco a discesa **Media Type** (Tipo di supporto) selezionare **HTTP Large Object** (LOB HTTP).

	![Tipo di supporto con LOB HTTP selezionato](./media/cdn-real-time-alerts/cdn-http-large.png)  

	> [AZURE.IMPORTANT] È necessario selezionare **HTTP Large Object** (LOB HTTP) come **Media Type** (Tipo di supporto). Le altre opzioni non vengono usate dalla **rete CDN di Azure di Verizon**. Se non si seleziona **HTTP Large Object** (LOB HTTP), l'avviso non verrà mai attivato.

8. Per **Expression** (Espressione), creare un'espressione da monitorare selezionando valori per **Metric** (Metrica), **Operator** (Operatore) e **Trigger value** (Valore trigger).

	- Per **Metric** (Metrica), selezionare il tipo di condizione che si vuole monitorare. **Bandwidth Mbps** (Mbps larghezza di banda) è la quantità di larghezza di banda utilizzata, misurata in megabit al secondo. **Total Connections** (Connessioni totali) è il numero di connessioni HTTP simultanee ai server perimetrali. Per informazioni sulle definizioni dei vari stati della cache e dei codici di stato, vedere [Azure CDN Cache Status Codes](https://msdn.microsoft.com/library/mt759237.aspx) (Codici di stato della cache della rete CDN di Azure) e [Azure CDN HTTP Status Codes](https://msdn.microsoft.com/library/mt759238.aspx) (Codici di stato HTTP della rete CDN di Azure).
	- **Operator** (Operatore) è l'operatore matematico che stabilisce la relazione tra la metrica e il valore trigger.
	- **Trigger Value** (Valore trigger) è il valore soglia da soddisfare perché venga inviata una notifica.

	Nell'esempio seguente l'espressione creata indica che si vuole ricevere una notifica quando il numero di codici di stato 404 è maggiore di 25.

	![Espressione di esempio di avviso in tempo reale](./media/cdn-real-time-alerts/cdn-expression.png)  

9. Per **Interval** (Intervallo), immettere la frequenza con cui l'espressione deve essere valutata.

10. Nell'elenco a discesa **Notify on** (Notifica per) selezionare per cosa si vuole ricevere una notifica quando l'espressione è true.
	
	- **Condition Start** (Inizio condizione) indica che verrà inviata una notifica la prima volta che viene rilevata la condizione specificata.
	- **Condition End** (Fine condizione) indica che verrà inviata una notifica quando la condizione specificata non viene più rilevata. Questa notifica può essere attivata solo dopo che il sistema di monitoraggio della rete ha rilevato che si è verificata la condizione specificata.
	- **Continuous** (Continuo) indica che verrà inviata una notifica ogni volta che il sistema di monitoraggio della rete rileva la condizione specificata. Tenere presente che il sistema di monitoraggio della rete cerca la condizione specificata solo una volta per ogni intervallo.
	- **Condition Start and End** (Inizio e fine condizione) indica che verrà inviata una notifica la prima volta che viene rilevata la condizione specificata e ancora una volta quando la condizione non viene più rilevata.

11. Per ricevere le notifiche tramite posta elettronica, selezionare la casella di controllo **Notify by Email** (Notifica tramite posta elettronica).

	![Form per la notifica tramite posta elettronica](./media/cdn-real-time-alerts/cdn-notify-email.png)
	
	Nel campo **To** (A) immettere l'indirizzo di posta elettronica a cui devono essere inviate le notifiche. Per **Subject** (Oggetto) e **Body** (Corpo), è possibile lasciare i valori predefiniti oppure personalizzare il messaggio usando l'elenco **Available keywords** (Parole chiave disponibili) per inserire in modo dinamico i dati dell'avviso quando il messaggio viene inviato.

	> [AZURE.NOTE] Per testare la notifica tramite posta elettronica è possibile fare clic sul pulsante **Test Notification** (Notifica di prova), ma solo dopo aver salvato la configurazione degli avvisi.

12. Per pubblicare le notifiche in un server Web, selezionare la casella di controllo **Notify by HTTP Post** (Notifica tramite HTTP Post).

	![Form per la notifica tramite HTTP Post](./media/cdn-real-time-alerts/cdn-notify-http.png)  

	Nel campo **Url** immettere l'URL in cui deve essere pubblicato il messaggio HTTP. Nella casella di testo **Headers** (Intestazioni) immettere le intestazioni HTTP da inviare nella richiesta. Per **Body** (Corpo), è possibile personalizzare il messaggio usando l'elenco **Available keywords** (Parole chiave disponibili) per inserire in modo dinamico i dati dell'avviso quando il messaggio viene inviato. L'impostazione predefinita per **Headers** (Intestazioni) e **Body** (Corpo) è un payload XML simile all'esempio seguente.

	```
	<string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
		<![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
	</string>
	```

	> [AZURE.NOTE] Per testare la notifica tramite HTTP Post è possibile fare clic sul pulsante **Test Notification** (Notifica di prova), ma solo dopo aver salvato la configurazione degli avvisi.

13. Fare clic sul pulsante **Save** (Salva) per salvare la configurazione degli avvisi. Se al passaggio 5 è stata selezionata l'opzione **Alert Enabled** (Avviso abilitato), ora l'avviso è attivo.

## Passaggi successivi

- Vedere [Statistiche in tempo reale nella rete CDN di Microsoft Azure](cdn-real-time-stats.md)
- Per un'analisi più approfondita, vedere [Report HTTP avanzati nella rete CDN di Microsoft Azure](cdn-advanced-http-reports.md)
- Vedere [Analizzare i modelli di utilizzo della rete CDN di Azure](cdn-analyze-usage-patterns.md)

<!---HONumber=AcomDC_0824_2016-->
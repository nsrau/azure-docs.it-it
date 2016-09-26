<properties
	pageTitle="Trasferire la sottoscrizione di Azure a un'altra offerta | Microsoft Azure"
	description="Informazioni su come modificare la sottoscrizione di Azure e passare a un'offerta diversa tramite il portale di gestione della sottoscrizione"
	services=""
	documentationCenter=""
	authors="genlin"
	manager="msmbaldwin"
	editor=""
	tags="billing,top-support-issue"/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="genli"/>

# Trasferire la sottoscrizione di Azure a un'altra offerta

## Informazioni importanti

È possibile che si sia [idonei](#where-is-the-button) a trasferire la sottoscrizione esistente a un'offerta più favorevole senza tempi di inattività del servizio. Di seguito sono riportati i percorsi attualmente supportati nel [Centro account](https://account.windowsazure.com/Subscriptions). Per altre informazioni sui requisiti fare clic sui collegamenti.

| Da | To |
|-------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Sviluppo/test con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0023p/) |
| [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Pagamento anticipato di 12 mesi](https://azure.microsoft.com/offers/ms-azr-0026p/) |
| [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |
| [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |
| [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) | [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/) |
| [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |
| [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |
> [AZURE.NOTE] Se si sta usando la versione di prova gratuita, leggere le informazioni su come [eseguire l'aggiornamento al servizio con pagamento in base al consumo](billing-buy-sign-up-azure-subscription.md#upgrade-azure-free-trial-to-pay-as-you-go).

> Se la sottoscrizione è stata eseguita recentemente, è necessario attendere fino al completamento del primo periodo di fatturazione prima di poter cambiare le offerte.

> Per altre modifiche relative alle offerte, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
	
## Guida al trasferimento della sottoscrizione a un'altra offerta

> [AZURE.VIDEO switch-to-a-different-azure-offer]

1.	Accedere al [Centro account di Azure](https://account.windowsazure.com/Subscriptions).
2.	Selezionare la sottoscrizione da modificare.
3.	Fare clic su **Passa a un'altra offerta**. Cosa fare se [il pulsante non è visibile](#where-is-the-button).

	![siwtchbutton](./media/billing-how-to-switch-azure-offer/switchbutton.png)
	
4.	Verranno visualizzate tutte le offerte a cui può essere trasferita la propria sottoscrizione. L'elenco varia in base agli abbonamenti a cui è associato l'account, nonché in base all'offerta corrente. Per informazioni dettagliate, fare clic sul collegamento **Altre informazioni** sotto ogni offerta. Per procedere al passaggio successivo fare clic sull'offerta desiderata.

	![selectoffer](./media/billing-how-to-switch-azure-offer/selectoffer.png)
5.	A seconda dell'offerta a cui si passa, può essere visualizzata una nota relativa all'impatto del passaggio. Leggere attentamente l'elenco e seguire le istruzioni prima di procedere.

	![thingstonote](./media/billing-how-to-switch-azure-offer/thingstonote.png)
6.	È possibile rinominare la sottoscrizione. Per impostazione predefinita, viene impostata sul nuovo nome dell'offerta. Fare clic su **Cambia offerta** per completare il processo.

	![confirmpage](./media/billing-how-to-switch-azure-offer/confirmpage.png)
7.	Completamento della procedura La sottoscrizione viene ora trasferita alla nuova offerta.

## Domande frequenti (FAQ)

### Quali modifiche delle offerte sono supportate?

Vedere l'elenco delle [offerte disponibili a cui è possibile passare](#what-you-need-to-know).

### Dove si trova il pulsante?

Il pulsante **Passa a un'altra offerta** potrebbe non essere visibile se:

- Il primo periodo di fatturazione non è ancora terminato ed è necessario attenderne il completamento prima di poter cambiare le offerte.

- Non è consentito modificare l'offerta. Controllare l'elenco di [offerte disponibili a cui è possibile passare](#what-you-need-to-know) per assicurarsi che la modifica desiderata sia supportata.

### Il passaggio a un'altra offerta ha un impatto sul servizio?

Non sono previsti tempi di inattività del servizio. Tuttavia, è possibile che l'offerta a cui si passa preveda restrizioni. Ad esempio, alcune offerte impediscono l'uso in produzione. In questo caso, è necessario spostare le risorse di produzione a un'altra sottoscrizione.

### Il passaggio a un'altra offerta ha un impatto sugli amministratori del servizio? 

Non è previsto alcun impatto sugli utenti associati alla sottoscrizione.

### Quali modifiche relative alla fatturazione sono previste quando si passa a un'altra offerta?

Il giorno del passaggio viene generata una fattura per tutti gli addebiti in sospeso. In seguito, gli addebiti per la sottoscrizione vengono eseguiti in base alle condizioni tariffarie della nuova offerta. La ricorrenza di fatturazione della sottoscrizione viene modificata alla data in cui è stato eseguito il passaggio di offerte. I dati relativi alla fatturazione e all'utilizzo prima del passaggio dell'offerta non vengono conservati, quindi è consigliabile scaricarli prima del passaggio.

> [AZURE.NOTE] A causa delle limitazioni relative alla fatturazione, le modifiche di offerta non sono possibili durante il primo ciclo di fatturazione dopo la creazione di una sottoscrizione.

### È possibile usare questo meccanismo per eseguire la migrazione da un'offerta Web diretta all'offerta [Cloud Solution Provider](https://partner.microsoft.com/Solutions/cloud-reseller-overview) (CSP) o al [Contratto Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/)?

Questo meccanismo può essere usato solo per passare da/a offerte Web dirette. Per migrare la sottoscrizione esistente a un Contratto Enterprise, chiedere all'amministratore dell'iscrizione di aggiungere l'account al Contratto Enterprise. Si riceverà quindi un invito tramite posta elettronica. Quando si seguono le istruzioni per accettare l'invito, le sottoscrizioni verranno spostate automaticamente al Contratto Enterprise. Al momento non è possibile trasferire una sottoscrizione diretta Web esistente all'offerta CSP.

## Passaggi successivi

- Informazioni su come [gestire i ruoli di amministratore](billing-add-change-azure-subscription-administrator.md) per la sottoscrizione

- Tenere traccia dell'uso [scaricando i dati sull'uso e la fattura](billing-download-azure-invoice-daily-usage-date.md)

> [AZURE.NOTE] Per altre domande, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

<!---HONumber=AcomDC_0914_2016-->
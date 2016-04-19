<properties
	pageTitle="Azure Insights: usare le azioni di scalabilità automatica per inviare notifiche di avviso di webhook e posta elettronica in Azure Insights| Microsoft Azure"
	description="Informazioni su come usare le azioni di scalabilità automatica per chiamare URL Web o inviare notifiche di posta elettronica in Azure Insights."
	authors="kamathashwin"
	manager=""
	editor=""
	services="azure-portal"
	documentationCenter="na"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="ashwink"/>

# Usare le azioni di scalabilità automatica per inviare notifiche di avviso di webhook e posta elettronica in Azure Insights

Questo articolo illustra come configurare i trigger per poter chiamare URL Web specifici o inviare messaggi di posta elettronica in base alle azioni di scalabilità automatica in Azure.

## Webhook
I webhook consentono di instradare le notifiche di avviso di Azure ad altri sistemi per la post-elaborazione o le notifiche personalizzate. È possibile, ad esempio, eseguire il routing degli avvisi a servizi che possono gestire una richiesta Web in ingresso per inviare SMS, registrare bug, inviare notifiche a un team usando servizi di messaggistica o chat e così via. L'URI del webhook deve essere un endpoint HTTP o HTTPS valido.

## Email
È possibile inviare un messaggio di posta elettronica a qualsiasi indirizzo di posta elettronica valido. Verrà inviata una notifica anche agli amministratori e ai coamministratori della sottoscrizione in cui viene eseguita la regola.


## Servizi cloud e app Web
È possibile acconsentire esplicitamente dal portale di Azure ai servizi cloud e alle server farm (app Web).

- Scegliere la metrica **Ridimensiona di**.

![Opzione Ridimensiona di](./media/insights-autoscale-to-webhook-email/insights-autoscale-scale-by.png)

## Set di scalabilità di macchine virtuali
Per le nuove macchine virtuali basate su Azure Resource Manager (set di scalabilità di macchine virtuali), è possibile eseguire la configurazione usando l'API REST, PowerShell e l'interfaccia della riga di comando. Un'interfaccia del portale non è ancora disponibile.


## Autenticazione nei webhook
Esistono due tipi di URI di autenticazione:

	1. Token-base authentication, where you save the webhook URI with a token ID as a query parameter. For example, https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue
	2. Basic authentication, where you use a user ID and password. For example, https://userid:password@mysamplealert/webcallback?someparamater=somevalue&parameter=value

## Schema di payload del webhook di notifica di scalabilità automatica
Quando viene generata la notifica di scalabilità automatica, nel payload del webhook vengono inclusi i metadati seguenti:

```
{
        "version": "1.0",
        "status": "Activated",
        "operation": "Scale In",
        "context": {
                "timestamp": "2016-03-11T07:31:04.5834118Z",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
                "name": "myautoscaleSetting",
                "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
                "subscriptionId": "s1",
                "resourceGroupName": "rg1",
                "resourceName": "MyCSRole",
                "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
                "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
                "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
                "oldCapacity": "3",
                "newCapacity": "2"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```


|Campo |Obbligatorio?|	Descrizione|
|---|---|---|
|status |yes |Stato che indica che è stata generata un'azione di scalabilità automatica|
|operation|	yes |Per un aumento delle istanze, sarà "Scale Out", mentre per una riduzione delle istanze, sarà "Scale In"|
|contesto|	yes |Contesto dell'azione di scalabilità automatica|
|timestamp|	yes |Timestamp in cui è stata attivata l'azione di scalabilità automatica|
|id |Sì|	ID Azure Resource Manager dell'impostazione di scalabilità automatica|
|name |Sì|	Nome dell'impostazione di scalabilità automatica|
|informazioni dettagliate|	Sì |Spiegazione dell'azione eseguita dal servizio di scalabilità automatica e della modifica al conteggio delle istanze|
|subscriptionId|	Sì |ID sottoscrizione della risorsa di destinazione da ridimensionare|
|resourceGroupName|	Sì|	Nome del gruppo di risorse della risorsa di destinazione da ridimensionare|
|resourceName |Sì|	Nome della risorsa di destinazione da ridimensionare|
|resourceType |Sì|	I tre valori supportati: "microsoft.classiccompute/domainnames/slots/roles" (ruoli dei servizi cloud), "microsoft.compute/virtualmachinescalesets" (set di scalabilità di macchine virtuali) e "Microsoft.Web/serverfarms" (app Web)|
|resourceId |Sì|ID Azure Resource Manager della risorsa di destinazione da ridimensionare|
|portalLink |Sì |Collegamento del portale di Azure alla pagina di riepilogo della risorsa di destinazione|
|oldCapacity|	Sì |Conteggio delle istanze corrente (precedente) quando la scalabilità automatica ha eseguito un'azione di scalabilità|
|newCapacity|	Sì |Nuovo conteggio delle istanze in base al quale la scalabilità automatica ha ridimensionato la risorsa|
|Proprietà|	No|	Facoltativo. Set di <Key  Value> coppie (ad esempio, Dictionary <String  String>). Il campo properties è facoltativo. In un flusso di lavoro basato su interfaccia utente personalizzata o app per la logica, è possibile immettere chiavi e valori che possono essere passati usando il payload. Un metodo alternativo per passare le proprietà personalizzate alla chiamata al webhook in uscita è di usare l'URI del webhook stesso (sotto forma di parametri di query)|

<!---HONumber=AcomDC_0330_2016-->
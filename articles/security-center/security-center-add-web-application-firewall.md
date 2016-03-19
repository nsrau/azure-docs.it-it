<properties
   pageTitle="Aggiungere un Web application firewall al Centro sicurezza di Azure | Microsoft Azure"
   description="Questo argomento illustra come implementare la raccomandazione del Centro sicurezza di Azure **Aggiungere un Web application firewall**."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/23/2016"
   ms.author="terrylan"/>

# Aggiungere un Web application firewall al Centro sicurezza di Azure

È possibile che il Centro sicurezza di Azure consigli di aggiungere un WAF (Web Application Firewall) di un partner Microsoft per proteggere le applicazioni Web. In questo documento è riportato un esempio di come eseguire questa operazione.

> [AZURE.NOTE] Le informazioni contenute in questo documento si applicano alla versione di anteprima del Centro sicurezza di Azure. Il documento introduce il servizio usando una distribuzione di esempio. Questa non è una guida dettagliata.

## Implementare la raccomandazione

1. Nel pannello **Raccomandazioni** selezionare **Proteggere le applicazioni Web mediante Web application firewall**. ![][1]

2. Nel pannello **Proteggere le applicazioni Web mediante Web application firewall** selezionare un'applicazione Web. Viene visualizzato il pannello **Aggiungere un Web application firewall**. ![][2]
3. È possibile scegliere di usare un Web application firewall esistente eventualmente disponibile o di crearne uno nuovo. In questo esempio non sono disponibili WAF esistenti ed è necessario crearne uno nuovo.

4. Per creare un nuovo WAF, selezionare una soluzione dall'elenco di partner integrati. In questo esempio viene selezionato **Barracuda Web Application Firewall**.
5. Viene visualizzato il pannello **Barracuda Web Application Firewall**, dove sono disponibili informazioni sulla soluzione del partner. Selezionare **Crea** nel pannello informativo. ![][3]

6. Viene visualizzato il pannello **Nuovo Web Application Firewall**, in cui è possibile eseguire la procedura di **Configurazione macchina virtuale** e fornire i dati richiesti in **Informazioni WAF**. Selezionare **Configurazione macchina virtuale**.

7. Nel pannello **Configurazione macchina virtuale** immettere le informazioni necessarie per avviare la macchina virtuale che eseguirà il WAF. ![][4]
8. Tornare al pannello **Nuovo Web Application Firewall** e selezionare **Informazioni WAF**. Nel pannello **Informazioni WAF** è possibile configurare il Web Application Firewall. Il passaggio 7 consente di configurare la macchina virtuale che eseguirà il WAF, mentre con il passaggio 8 si esegue il provisioning del WAF stesso.

9. Tornare al pannello **Raccomandazioni**. Dopo la creazione del WAF viene aggiunta una nuova voce: **Finalizzare la configurazione di Web application firewall**. Questa raccomandazione informa l'utente che è necessario completare il processo di connessione effettiva del WAF all'interno della rete virtuale di Azure in modo da proteggere l'applicazione. ![][5]

10. Selezionare **Finalizzare la configurazione di Web application firewall**. Viene visualizzato un nuovo pannello. Come si può vedere, è presente un'applicazione Web il cui traffico deve essere reindirizzato.
11. Selezionare l'applicazione Web. Viene visualizzato un pannello in cui è possibile eseguire la procedura per finalizzare la configurazione del Web application firewall. Completare i passaggi e quindi selezionare **Limita il traffico**. Il Centro sicurezza eseguirà quindi la connessione automaticamente. ![][6]

> [AZURE.NOTE] Il processo di provisioning automatico si basa su pacchetti WAF, creati con il modello di distribuzione di Resource Manager, che vengono distribuiti in una rete virtuale separata. L'accesso alle applicazioni Web protette presenti in macchine virtuali (versione classica) viene limitato ai dispositivi WAF solo tramite NSG. In futuro tale supporto verrà esteso a una distribuzione completamente personalizzata di pacchetti WAF (versione classica). È consigliabile leggere altre informazioni sui [modelli di distribuzione di Azure Resource Manager e classico](../azure-classic-rm.md) per le risorse di Azure.

I log generati dal WAF sono ora completamente integrati. Il Centro sicurezza può avviare automaticamente la raccolta e l'analisi dei log, per mettere in evidenza eventuali avvisi di sicurezza importanti.

## Passaggi successivi

In questo documento è stato illustrato come implementare la raccomandazione "Aggiungere un Web application firewall". Per altre informazioni sulla configurazione di un Web application firewall, vedere gli argomenti seguenti:

- [Configurazione di un Web application firewall (WAF) per l'ambiente del servizio app](../app-service-web/app-service-app-service-environment-web-application-firewall.md)

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

- [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md): informazioni su come configurare i criteri di sicurezza.
- [Monitoraggio dello stato della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.
- [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza.
- [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md): informazioni sul modo in cui le raccomandazioni facilitano la protezione delle risorse di Azure.
- [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'utilizzo del servizio.
- [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): post di blog sulla sicurezza e sulla conformità di Azure.

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]: ./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png

<!---HONumber=AcomDC_0224_2016-->
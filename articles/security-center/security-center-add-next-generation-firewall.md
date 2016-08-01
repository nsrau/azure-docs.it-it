<properties
   pageTitle="Aggiungere un firewall di nuova generazione nel Centro sicurezza di Azure | Microsoft Azure"
   description="Questo argomento illustra come implementare la raccomandazione **Aggiungi un firewall di nuova generazione** del Centro sicurezza di Azure."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/15/2016"
   ms.author="terrylan"/>

# Aggiungere un firewall di nuova generazione in Centro sicurezza di Azure

È possibile che il Centro sicurezza di Azure consigli di aggiungere un firewall di nuova generazione di un partner Microsoft per aumentare la protezione delle applicazioni Web. In questo documento è riportato un esempio di come eseguire questa operazione.

> [AZURE.NOTE] Le informazioni contenute in questo documento si applicano alla versione di anteprima del Centro sicurezza di Azure. Il documento introduce il servizio usando una distribuzione di esempio. Questa non è una guida dettagliata.

## Implementare la raccomandazione

1. Nel pannello **Raccomandazioni** selezionare **Aggiungi un firewall di nuova generazione**. ![Aggiungi un firewall di nuova generazione][1]

2. Nel pannello **Aggiungi un firewall di nuova generazione** selezionare un endpoint. ![Selezionare un endpoint][2]

3. Viene visualizzato un secondo pannello **Aggiungi un firewall di nuova generazione**. È possibile scegliere di usare una soluzione esistente eventualmente disponibile o di crearne una nuova. In questo esempio non sono disponibili soluzioni esistenti ed è necessario creare un firewall di nuova generazione.![Creare un firewall di nuova generazione][3]

4. Per creare un nuovo firewall di nuova generazione, selezionare una soluzione dall'elenco di partner integrati. In questo esempio si selezionerà **Check Point**. ![Selezionare un firewall di nuova generazione][4]

5. Viene visualizzato il pannello **Check Point**, dove sono disponibili informazioni sulla soluzione del partner. Selezionare **Crea** nel pannello informativo. ![Pannello di informazioni sul firewall][5]

6. Viene visualizzato il pannello **Crea macchina virtuale**. Qui è possibile immettere le informazioni necessarie per creare una macchina virtuale che eseguirà il firewall di nuova generazione. Seguire i passaggi e fornire le informazioni necessarie sul firewall di nuova generazione. Selezionare OK per applicare.![Creare la macchina virtuale per eseguire il firewall di nuova generazione][6]

## Passaggi successivi

Questo documento illustra come implementare la raccomandazione "Aggiungi un firewall di nuova generazione" del Centro sicurezza. Per ulteriori informazioni sui firewall di nuova generazione e la soluzione del partner Check Point, vedere gli argomenti seguenti:

- [Firewall di nuova generazione](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
- [Check Point vSEC](https://azure.microsoft.com/marketplace/partners/checkpoint/check-point-r77-10/)

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

- [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md): informazioni su come configurare i criteri di sicurezza.
- [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md): informazioni sul modo in cui le raccomandazioni semplificano la protezione delle risorse di Azure.
- [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.
- [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza.
- [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md): informazioni su come monitorare lo stato integrità delle soluzioni dei partner.
- [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.
- [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): post di blog sulla sicurezza e sulla conformità di Azure.

<!--Image references-->
[1]: ./media/security-center-add-next-gen-firewall/add-next-gen-firewall.png
[2]: ./media/security-center-add-next-gen-firewall/select-an-endpoint.png
[3]: ./media/security-center-add-next-gen-firewall/create-new-next-gen-firewall.png
[4]: ./media/security-center-add-next-gen-firewall/select-next-gen-firewall.png
[5]: ./media/security-center-add-next-gen-firewall/firewall-solution-info-blade.png
[6]: ./media/security-center-add-next-gen-firewall/create-virtual-machine.png

<!---HONumber=AcomDC_0720_2016-->
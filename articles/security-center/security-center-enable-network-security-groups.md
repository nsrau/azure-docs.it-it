<properties
   pageTitle="Abilitare i gruppi di sicurezza di rete nel Centro sicurezza di Azure | Microsoft Azure"
   description="Questo documento illustra come implementare la raccomandazione **Abilita i gruppi di sicurezza di rete** del Centro sicurezza di Azure."
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
   ms.date="07/21/2016"
   ms.author="terrylan"/>

# Abilitare i gruppi di sicurezza di rete nel Centro sicurezza di Azure

Se non è già disponibile, il Centro sicurezza di Azure consiglierà l'abilitazione di un gruppo di sicurezza di rete. I gruppi di sicurezza di rete contengono un elenco di regole dell'elenco di controllo di accesso (ACL) che consentono o rifiutano il traffico di rete alle istanze VM in una rete virtuale. I gruppi di sicurezza di rete possono essere associati a subnet o singole istanze VM in una subnet. Quando un gruppo di sicurezza di rete viene associato a una subnet, le regole dell'elenco di controllo di accesso si applicano a tutte le istanze VM in tale subnet. Inoltre il traffico verso una singola VM può essere ulteriormente limitato associando un gruppo di sicurezza di rete direttamente a tale VM. Per altre informazioni, vedere [Che cos'è un gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md).

Se i gruppi di sicurezza di rete non sono stati abilitati, il Centro sicurezza presenterà all'utente due indicazioni, ovvero Abilita i gruppi di sicurezza di rete nelle subnet e Abilita i gruppi di sicurezza di rete sulle macchine virtuali. È possibile scegliere il livello a cui applicare i gruppi di sicurezza di rete, ovvero subnet o VM.


> [AZURE.NOTE] Il documento introduce il servizio usando una distribuzione di esempio. Questa non è una guida dettagliata.

## Implementare la raccomandazione

1. Nel pannello **Raccomandazioni** selezionare **Abilita i gruppi di sicurezza di rete** nelle subnet o sulle macchine virtuali. ![Abilitare i gruppi di sicurezza di rete][1]

2. Verrà visualizzato il pannello **Configura i gruppi di sicurezza di rete mancanti** per le subnet o le macchine virtuali, in base alla raccomandazione selezionata. Selezionare una subnet o una macchina virtuale su cui configurare un gruppo di sicurezza di rete.

  ![Configurare i gruppi di sicurezza di rete per la subnet][2]

  ![Configurare i gruppi di sicurezza di rete per le macchine virtuali][3]
3. Nel pannello **Scegli un gruppo di sicurezza di rete** selezionare un gruppo di sicurezza di rete o crearne uno nuovo.

  ![Scegliere un gruppo di sicurezza di rete][4]

Se si crea un nuovo gruppo di sicurezza di rete, seguire la procedura illustrata in [Come gestire gruppi di sicurezza di rete tramite il portale di Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md) per creare un gruppo di sicurezza di rete e configurare le regole di sicurezza.

## Vedere anche

Questo articolo ha illustrato come implementare la raccomandazione "Abilita i gruppi di sicurezza di rete" per subnet o macchine virtuali nel Centro sicurezza. Per altre informazioni sull'abilitazione dei gruppi di sicurezza di rete, vedere gli articoli seguenti:

- [Che cos'è un gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md)
- [Come gestire gruppi di sicurezza di rete tramite il portale di Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

- [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md): informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
- [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md): informazioni sul modo in cui le raccomandazioni semplificano la protezione delle risorse di Azure.
- [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.
- [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza.
- [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md): informazioni su come monitorare lo stato di integrità delle soluzioni dei partner.
- [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.
- [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): informazioni e notizie aggiornate sulla sicurezza di Azure.

<!--Image references-->
[1]: ./media/security-center-enable-nsg/enable-nsg.png
[2]: ./media/security-center-enable-nsg/configure-nsg-for-subnet.png
[3]: ./media/security-center-enable-nsg/configure-nsg-for-vm.png
[4]: ./media/security-center-enable-nsg/choose-nsg.png

<!---HONumber=AcomDC_0727_2016-->
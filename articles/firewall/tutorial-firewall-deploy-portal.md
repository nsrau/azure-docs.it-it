---
title: Distribuire e configurare Firewall di Azure tramite il portale di Azure
description: Questa esercitazione mostra come distribuire e configurare Firewall di Azure tramite il portale di Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 10/30/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 47a04df843ec307b54cc1d6597f9a3cf8668e291
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50238829"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Esercitazione: Distribuire e configurare Firewall di Azure tramite il portale di Azure

Firewall di Azure ha due tipi di regole per controllare l'accesso in uscita:

- **Regole di applicazione**

   Consentono di configurare i nomi di dominio completo (FQDN) che sono accessibili da una subnet. Ad esempio, è possibile consentire l'accesso a *github.com* da una subnet.
- **Regole di rete**

   Consentono di configurare le regole che contengono l'indirizzo di origine, il protocollo, la porta di destinazione e l'indirizzo di destinazione. Ad esempio, è possibile creare una regola per consentire il traffico sulla porta 53 (DNS) all'indirizzo IP del server DNS da una subnet.

Il traffico di rete è sottoposto alle regole del firewall configurate quando si instrada il traffico di rete al firewall come gateway predefinito della subnet.

Le regole di applicazione e di rete vengono archiviate nelle *raccolte di regole*. Una raccolta di regole è un elenco di regole che condividono la stessa azione e priorità.  Una raccolta di regole di rete è un elenco di regole di rete, mentre una raccolta di regole di applicazione è un elenco di regole dell'applicazione.

Per altre informazioni sulla logica di elaborazione delle regole di Firewall di Azure, vedere [Azure Firewall rule processing logic](rule-processing.md) (Logica di elaborazione delle regole di Firewall di Azure).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare un ambiente di rete di test
> * Distribuire un firewall
> * Creare una route predefinita
> * Configurare le regole di applicazione
> * Configurare le regole di rete
> * Testare il firewall

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Per questa esercitazione viene creata una singola rete virtuale con tre subnet:
- **FW-SN**: in questa subnet si trova il firewall.
- **Workload-SN**: in questa subnet si trova il server del carico di lavoro. Il traffico di rete di questa subnet passa attraverso il firewall.
- **Jump-SN**: in questa subnet si trova il jump server. Il jump server ha un indirizzo IP pubblico a cui è possibile connettersi con Desktop remoto. Da qui è quindi possibile connettersi al server del carico di lavoro (tramite un'altra istanza di Desktop remoto).

![Infrastruttura di rete dell'esercitazione](media/tutorial-firewall-rules-portal/Tutorial_network.png)

Questa esercitazione usa una configurazione di rete semplificata per facilitare la distribuzione. Per le distribuzioni di produzione è consigliabile un [modello hub e spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke), in cui il firewall si trova nella propria rete virtuale e i server del carico di lavoro si trovano nelle reti virtuali associate all'interno della stessa area con una o più subnet.

## <a name="set-up-the-network-environment"></a>Configurare l'ambiente di rete

In primo luogo, creare un gruppo di risorse per contenere le risorse necessarie per distribuire il firewall. Creare quindi una rete virtuale, le subnet e i server di test.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

1. Accedere al portale di Azure all'indirizzo [http://portal.azure.com](http://portal.azure.com).
2. Nella home page del portale di Azure fare clic su **Gruppi di risorse**, quindi fare clic su **Aggiungi**.
3. In **Nome del gruppo di risorse** immettere **Test-FW-RG**.
4. In **Sottoscrizione** selezionare la propria sottoscrizione.
5. In **Località del gruppo di risorse** selezionare una località. Tutte le successive risorse create devono trovarsi nella stessa località.
6. Fare clic su **Create**(Crea).

### <a name="create-a-vnet"></a>Creare una rete virtuale

1. Nella home page del portale di Azure fare clic su **Tutti i servizi**.
2. In **Rete** fare clic su **Reti virtuali**.
3. Fare clic su **Aggiungi**.
4. In **Nome** immettere **Test-FW-VN**.
5. In **Spazio degli indirizzi** immettere **10.0.0.0/16**.
6. In **Sottoscrizione** selezionare la propria sottoscrizione.
7. In **Gruppo di risorse** selezionare **Usa esistente** e quindi selezionare **Test-FW-RG**.
8. In **Località** selezionare la stessa località usata in precedenza.
9. In **Subnet** immettere **AzureFirewallSubnet** per **Nome**. Il firewall si troverà in questa subnet e il nome della subnet **deve** essere AzureFirewallSubnet.
10. In **Intervallo indirizzi** immettere **10.0.1.0/24**.
11. Usare le altre impostazioni predefinite e quindi fare clic su **Crea**.

> [!NOTE]
> La dimensione minima della subnet AzureFirewallSubnet è /25.

### <a name="create-additional-subnets"></a>Creare subnet aggiuntive

Successivamente, creare le subnet per il jump server e una subnet per i server del carico di lavoro.

1. Nella home page del portale di Azure fare clic su **Gruppi di risorse**, quindi fare clic su **Test-FW-RG**.
2. Fare clic sulla rete virtuale **Test-FW-VN**.
3. Fare clic su **Subnet** e quindi su **+Subnet**.
4. In **Nome** immettere **Workload-SN**.
5. In **Intervallo indirizzi** immettere **10.0.2.0/24**.
6. Fare clic su **OK**.

Creare un'altra subnet denominata **Jump-NS** e con **10.0.3.0/24** come intervallo indirizzi.

### <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare ora le macchine virtuali per il jump server e il server del carico di lavoro e posizionarle nelle subnet appropriate.

1. Nella home page del portale di Azure fare clic su **Tutti i servizi**.
2. In **Calcolo** fare clic su **Macchine virtuali**.
3. Fare clic su **Aggiungi**, su **Windows Server**, su **Windows Server 2016 Datacenter** e quindi fare clic su **Crea**.

**Nozioni di base**

1. In **Nome** immettere **Srv-Jump**.
5. Immettere un nome utente e la password.
6. In **Sottoscrizione** selezionare la propria sottoscrizione.
7. In **Gruppo di risorse** fare clic su **Usa esistente** e quindi selezionare **Test-FW-RG**.
8. In **Località** selezionare la stessa località usata in precedenza.
9. Fare clic su **OK**.

**Dimensione**

1. Scegliere le dimensioni appropriate per una macchina virtuale di test che esegue Windows Server. Ad esempio, **B2ms** (8 GB di RAM, 16 GB di archiviazione).
2. Fare clic su **Seleziona**.

**Impostazioni**

1. In **Rete** selezionare **Test-FW-VN** per **Rete virtuale**.
2. In **Subnet** selezionare **Jump-SN**.
3. In **Selezionare le porte in ingresso pubbliche** selezionare **RDP (3389)**. 

    È opportuno limitare l'accesso all'indirizzo IP pubblico, ma è necessario aprire la porta 3389 per la connessione di una sessione di Desktop remoto al jump server. 
2. Lasciare le altre impostazioni predefinite e fare clic su **OK**.

**Summary**

Controllare il riepilogo e quindi fare clic su **Crea**. Questa operazione richiederà qualche minuto.

Ripetere questo processo per creare un'altra macchina virtuale denominata **Srv-Work**.

Usare le informazioni nella tabella seguente per configurare le **Impostazioni** per la macchina virtuale Srv-Work. Il resto della configurazione è uguale a quella della macchina virtuale Srv-Jump.

|Impostazione  |Valore  |
|---------|---------|
|Subnet|Workload-SN|
|Indirizzo IP pubblico|Nessuna|
|Selezionare le porte in ingresso pubbliche|Nessuna porta in ingresso pubblica|

## <a name="deploy-the-firewall"></a>Distribuire il firewall

1. Nella home page del portale fare clic su **Crea una risorsa**.
2. Fare clic su **Rete** e su **In primo piano** e quindi fare clic su **Visualizza tutto**.
3. Fare clic su **Firewall** e quindi su **Crea**. 
4. Nella pagina **Crea un firewall** usare la tabella seguente per configurare il firewall:
   
   |Impostazione  |Valore  |
   |---------|---------|
   |NOME     |Test-FW01|
   |Sottoscrizione     |\<sottoscrizione in uso\>|
   |Gruppo di risorse     |**Usa esistente**: Test-FW-RG |
   |Località     |Selezionare la stessa località usata in precedenza|
   |Scegliere una rete virtuale     |**Usa esistente**: Test-FW-VN|
   |Indirizzo IP pubblico     |**Creare un nuovo gruppo di risorse**. L'indirizzo IP pubblico deve essere di tipo SKU Standard.|

2. Fare clic su **Rivedi e crea**.
3. Controllare il riepilogo e quindi fare clic su **Crea** per creare il firewall.

   La distribuzione richiederà qualche minuto.
4. Al termine della distribuzione, passare al gruppo di risorse **Test-FW-RG** e fare clic sul firewall **Test FW01**.
6. Annotare l'indirizzo IP privato. Sarà necessario più avanti per la creazione della route predefinita.

## <a name="create-a-default-route"></a>Creare una route predefinita

Per la subnet **Workload-SN** configurare la route predefinita in uscita per passare attraverso il firewall.

1. Nella home page del portale di Azure fare clic su **Tutti i servizi**.
2. In **Rete** fare clic su **Tabelle route**.
3. Fare clic su **Aggiungi**.
4. In **Nome** immettere **Firewall-route**.
5. In **Sottoscrizione** selezionare la propria sottoscrizione.
6. In **Gruppo di risorse** selezionare **Usa esistente** e quindi selezionare **Test-FW-RG**.
7. In **Località** selezionare la stessa località usata in precedenza.
8. Fare clic su **Create**(Crea).
9. Fare clic su **Aggiorna** e quindi fare clic sulla tabella di route **Firewall-route**.
10. Fare clic su **Subnet** e quindi su **Associa**.
11. Fare clic su **Rete virtuale** e quindi selezionare **Test-FW-VN**.
12. In **Subnet** fare clic su **Workload-SN**.

    > [!IMPORTANT]
    > Assicurarsi di selezionare solo la subnet **Workload-SN** per questa route; in caso contrario, il firewall non funzionerà correttamente.

13. Fare clic su **OK**.
14. Fare clic su **Route** e quindi su **Aggiungi**.
15. In **Nome route** immettere **FW-DG**.
16. In **Prefisso indirizzo** immettere **0.0.0.0/0**.
17. In **Tipo hop successivo** selezionare **Appliance virtuale**.

    Firewall di Azure è in effetti un servizio gestito, ma in questa situazione è possibile usare un'appliance virtuale.
18. In **Indirizzo hop successivo** immettere l'indirizzo IP privato per il firewall annotato in precedenza.
19. Fare clic su **OK**.

## <a name="configure-application-rules"></a>Configurare le regole di applicazione

1. Aprire **Test-FW-RG** e fare clic sul firewall **Test-FW01**.
2. Nella pagina **Test-FW01** fare clic su **Regole** in **Impostazioni**.
3. Fare clic su **Aggiungi raccolta regole dell'applicazione**.
4. In **Nome** immettere **App-Coll01**.
5. In **Priorità** immettere **200**.
6. In **Azione** selezionare **Consenti**.
7. In **Regole** immettere **AllowGH** in **Nome**.
8. In **Indirizzi di origine** immettere **10.0.2.0/24**.
9. In **Protocollo:Porta** immettere **http, https**. 
10. In **FQDN di destinazione** immettere **github.com**
11. Fare clic su **Aggiungi**.

Firewall di Azure include una raccolta di regole predefinite per i nomi di dominio completi dell'infrastruttura consentiti per impostazione predefinita. Questi nomi di dominio completi sono specifici per la piattaforma e non possono essere usati per altri scopi. Per altre informazioni, vedere [Infrastructure FQDNs](infrastructure-fqdns.md) (FQDN dell'infrastruttura).

## <a name="configure-network-rules"></a>Configurare le regole di rete

1. Fare clic su **Aggiungi raccolta regole di rete**.
2. In **Nome** immettere **Net-Coll01**.
3. In **Priorità** immettere **200**.
4. In **Azione** selezionare **Consenti**.

6. In **Regole** immettere **AllowDNS** in **Nome**.
8. In **Protocollo** selezionare **UDP**.
9. In **Indirizzi di origine** immettere **10.0.2.0/24**.
10. In Indirizzo di destinazione immettere **209.244.0.3,209.244.0.4**
11. In **Porte di destinazione** immettere **53**.
12. Fare clic su **Aggiungi**.

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Modificare l'indirizzo DNS primario e secondario per l'interfaccia di rete **Srv-Work**

Ai fini del test in questa esercitazione vengono configurati gli indirizzi DNS primari e secondari. Ciò non è un requisito generale di Firewall di Azure. 

1. Nel portale di Azure aprire il gruppo di risorse **Test-FW-RG**.
2. Fare clic sull'interfaccia di rete per la macchina virtuale **Srv-Work**.
3. In **Impostazioni** fare clic su **Server DNS**.
4. In **Server DNS** fare clic su **Personalizzato**.
5. Immettere **209.244.0.3** nella casella di testo **Aggiungi server DNS** e **209.244.0.4** nella casella di testo successiva.
6. Fare clic su **Save**. 
7. Riavviare la macchina virtuale **Srv-Work**.

## <a name="test-the-firewall"></a>Testare il firewall

1. Nel portale di Azure rivedere le impostazioni di rete per la macchina virtuale **Srv-Work** e annotare l'indirizzo IP privato.
2. Connettere una sessione di Desktop remoto alla macchina virtuale **Srv-Jump** e da qui aprire una connessione Desktop remoto all'indirizzo IP privato **Srv-Work**.

5. Aprire Internet Explorer e passare a http://github.com.
6. Fare clic su **OK** e **Chiudi** negli avvisi di sicurezza.

   Verrà visualizzata la home page di GitHub.

7. Passare a http://www.msn.com.

   Si verrà bloccati dal firewall.

A questo punto si è verificato che le regole del firewall funzionano:

- È possibile passare al nome di dominio completo consentito ma non agli altri.
- È possibile risolvere i nomi DNS con il server DNS esterno configurato.

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile conservare le risorse del firewall per l'esercitazione successiva oppure, se non è più necessario, eliminare il gruppo di risorse **Test-FW-RG** per eliminare tutte le risorse correlate al firewall.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Configurare la rete
> * Creare un firewall
> * Creare una route predefinita
> * Configurare le regole del firewall di applicazione e di rete
> * Testare il firewall

È possibile ora monitorare i log di Firewall di Azure.

> [!div class="nextstepaction"]
> [Esercitazione: Monitorare i log di Firewall di Azure](./tutorial-diagnostics.md)

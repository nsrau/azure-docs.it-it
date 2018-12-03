---
title: 'Esercitazione: Distribuire e configurare Firewall di Azure tramite il portale di Azure'
description: Questa esercitazione mostra come distribuire e configurare Firewall di Azure tramite il portale di Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 11/15/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 179a13d6fbb162ae7727c6a176b60879901dc4d1
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2018
ms.locfileid: "52426187"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Esercitazione: Distribuire e configurare Firewall di Azure tramite il portale di Azure

Il controllo dell'accesso alla rete in uscita è un componente importante di un piano di sicurezza della rete generale. Ad esempio, potrebbe essere utile limitare l'accesso ai siti Web o le porte e gli indirizzi IP in uscita a cui è possibile accedere.

È possibile controllare l'accesso alla rete in uscita da una subnet di Azure con Firewall di Azure. Con Firewall di Azure, è possibile configurare:

* Regole di applicazione che definiscono i nomi di dominio completi (FQDN) accessibili da una subnet.
* Regole di rete che definiscono l'indirizzo di origine, il protocollo, la porta di destinazione e l'indirizzo di destinazione.

Il traffico di rete è sottoposto alle regole del firewall configurate quando si instrada il traffico di rete al firewall come gateway predefinito della subnet.

Per questa esercitazione viene creata una singola rete virtuale semplificata con tre subnet per facilitare la distribuzione. Per le distribuzioni di produzione è consigliabile un [modello hub e spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke), in cui il firewall si trova nella propria rete virtuale e i server del carico di lavoro si trovano nelle reti virtuali associate all'interno della stessa area con una o più subnet.

- **AzureFirewallSubnet**: in questa subnet si trova il firewall.
- **Workload-SN**: in questa subnet si trova il server del carico di lavoro. Il traffico di rete di questa subnet passa attraverso il firewall.
- **Jump-SN**: in questa subnet si trova il jump server. Il jump server ha un indirizzo IP pubblico a cui è possibile connettersi con Desktop remoto. Da qui è quindi possibile connettersi al server del carico di lavoro (tramite un'altra istanza di Desktop remoto).

![Infrastruttura di rete dell'esercitazione](media/tutorial-firewall-rules-portal/Tutorial_network.png)

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare un ambiente di rete di test
> * Distribuire un firewall
> * Creare una route predefinita
> * Configurare un'applicazione per consentire l'accesso a github.com
> * Configurare una regola di rete per consentire l'accesso a server DNS esterni
> * Testare il firewall

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="set-up-the-network"></a>Configurare la rete

In primo luogo, creare un gruppo di risorse per contenere le risorse necessarie per distribuire il firewall. Creare quindi una rete virtuale, le subnet e i server di test.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Il gruppo di risorse contiene tutte le risorse per l'esercitazione.

1. Accedere al portale di Azure all'indirizzo [http://portal.azure.com](http://portal.azure.com).
2. Nella home page del portale di Azure fare clic su **Gruppi di risorse** > **Aggiungi**.
3. In **Nome del gruppo di risorse** immettere **Test-FW-RG**.
4. In **Sottoscrizione** selezionare la propria sottoscrizione.
5. In **Località del gruppo di risorse** selezionare una località. Tutte le successive risorse create devono trovarsi nella stessa località.
6. Fare clic su **Create**(Crea).

### <a name="create-a-vnet"></a>Creare una rete virtuale

Questa rete virtuale conterrà tre subnet.

1. Nella home page del portale di Azure fare clic su **Tutti i servizi**.
2. In **Rete** fare clic su **Reti virtuali**.
3. Fare clic su **Aggiungi**.
4. In **Nome** immettere **Test-FW-VN**.
5. In **Spazio degli indirizzi** immettere **10.0.0.0/16**.
6. In **Sottoscrizione** selezionare la propria sottoscrizione.
7. Per **Gruppo di risorse** selezionare **Usa esistente** > **Test-FW-RG**.
8. In **Località** selezionare la stessa località usata in precedenza.
9. In **Subnet** immettere **AzureFirewallSubnet** per **Nome**. Il firewall si troverà in questa subnet e il nome della subnet **deve** essere AzureFirewallSubnet.
10. In **Intervallo indirizzi** immettere **10.0.1.0/24**.
11. Usare le altre impostazioni predefinite e quindi fare clic su **Crea**.

> [!NOTE]
> La dimensione minima della subnet AzureFirewallSubnet è /26.

### <a name="create-additional-subnets"></a>Creare subnet aggiuntive

Successivamente, creare le subnet per il jump server e una subnet per i server del carico di lavoro.

1. Nella home page del portale di Azure fare clic su **Gruppi di risorse** > **Test-FW-RG**.
2. Fare clic sulla rete virtuale **Test-FW-VN**.
3. Fare clic su **Subnet** > **+Subnet**.
4. In **Nome** immettere **Workload-SN**.
5. In **Intervallo indirizzi** immettere **10.0.2.0/24**.
6. Fare clic su **OK**.

Creare un'altra subnet denominata **Jump-NS** e con **10.0.3.0/24** come intervallo indirizzi.

### <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare ora le macchine virtuali per il jump server e il server del carico di lavoro e posizionarle nelle subnet appropriate.

1. Nel portale di Azure fare clic su **Crea una risorsa**.
2. Fare clic su **Calcolo** e quindi selezionare **Windows Server 2016 Datacenter** nell'elenco In primo piano.
3. Immettere i valori seguenti per la macchina virtuale:

    - *Test-FW-RG* per il gruppo di risorse.
    - *Srv-Jump* come nome della macchina virtuale.
    - *azureuser* come nome utente dell'amministratore.
    - *Azure123456!* come password.

4. In **Regole porta in ingresso** fare clic su **Consenti porte selezionate** per **Porte in ingresso pubbliche**.
5. In **Selezionare le porte in ingresso** selezionare **RDP (3389)**.

6. Accettare tutte le altre impostazioni predefinite e fare clic su **Avanti: Dischi**.
7. Accettare le impostazioni predefinite del disco e fare clic su **Avanti: Rete**.
8. Assicurarsi che **Test-FW-VN** sia selezionato per la rete virtuale e che la subnet sia **Jump-SN**.
9. Per **IP pubblico** fare clic su **Crea nuovo**.
10. Digitare **Srv-Jump-PIP** per il nome dell'indirizzo IP pubblico e fare clic su **OK**.
11. Accettare tutte le altre impostazioni predefinite e fare clic su **Avanti: Gestione**.
12. Fare clic su **Disattivato** per disabilitare la diagnostica di avvio. Accettare tutte le altre impostazioni predefinite e fare clic su **Rivedi e crea**.
13. Verificare le impostazioni nella pagina di riepilogo e quindi fare clic su **Crea**.

Ripetere questo processo per creare un'altra macchina virtuale denominata **Srv-Work**.

Usare le informazioni nella tabella seguente per configurare le Impostazioni per la macchina virtuale Srv-Work. Il resto della configurazione è uguale a quella della macchina virtuale Srv-Jump.

|Impostazione  |Valore  |
|---------|---------|
|Subnet|Workload-SN|
|IP pubblico|Nessuna|
|Porte in ingresso pubbliche|Nessuna|

## <a name="deploy-the-firewall"></a>Distribuire il firewall

Distribuire il firewall nella rete virtuale.

1. Nella home page del portale fare clic su **Crea una risorsa**.
2. Fare clic su **Rete** e su **In primo piano** e quindi fare clic su **Visualizza tutto**.
3. Fare clic su **Firewall** > **Crea**. 
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
10. Fare clic su **Subnet** > **Associa**.
11. Fare clic su **Rete virtuale** > **Test-FW-VN**.
12. In **Subnet** fare clic su **Workload-SN**. Assicurarsi di selezionare solo la subnet **Workload-SN** per questa route; in caso contrario, il firewall non funzionerà correttamente.

13. Fare clic su **OK**.
14. Fare clic su **Route** > **Aggiungi**.
15. In **Nome route** immettere **FW-DG**.
16. In **Prefisso indirizzo** immettere **0.0.0.0/0**.
17. In **Tipo hop successivo** selezionare **Appliance virtuale**.

    Firewall di Azure è in effetti un servizio gestito, ma in questa situazione è possibile usare un'appliance virtuale.
18. In **Indirizzo hop successivo** immettere l'indirizzo IP privato per il firewall annotato in precedenza.
19. Fare clic su **OK**.

## <a name="configure-an-application-rule"></a>Configurare una regola di applicazione

Si tratta della regola di applicazione che consente l'accesso in uscita a github.com.

1. Aprire **Test-FW-RG** e fare clic sul firewall **Test-FW01**.
2. Nella pagina **Test-FW01** fare clic su **Regole** in **Impostazioni**.
3. Fare clic sulla scheda **Raccolta regole dell'applicazione**.
4. Fare clic su **Aggiungi raccolta regole dell'applicazione**.
5. In **Nome** immettere **App-Coll01**.
6. In **Priorità** immettere **200**.
7. In **Azione** selezionare **Consenti**.
8. In **Regole**, **FQDN di destinazione**, immettere **AllowGH** in **Nome**.
9. In **Indirizzi di origine** immettere **10.0.2.0/24**.
10. In **Protocollo:Porta** immettere **http, https**.
11. In **FQDN di destinazione** immettere **github.com**
12. Fare clic su **Aggiungi**.

Firewall di Azure include una raccolta di regole predefinite per i nomi di dominio completi dell'infrastruttura consentiti per impostazione predefinita. Questi nomi di dominio completi sono specifici per la piattaforma e non possono essere usati per altri scopi. Per altre informazioni, vedere [Infrastructure FQDNs](infrastructure-fqdns.md) (FQDN dell'infrastruttura).

## <a name="configure-a-network-rule"></a>Configurare una regola di rete

Si tratta della regola di rete che consente l'accesso in uscita a due indirizzi IP sulla porta 53 (DNS).

1. Fare clic sulla scheda **Raccolta regole di rete**.
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

A questo punto testare il firewall per verificare che funzioni come previsto.

1. Nel portale di Azure rivedere le impostazioni di rete per la macchina virtuale **Srv-Work** e annotare l'indirizzo IP privato.
2. Connettere una sessione di Desktop remoto alla macchina virtuale **Srv-Jump** e da qui aprire una connessione Desktop remoto all'indirizzo IP privato **Srv-Work**.

5. Aprire Internet Explorer e passare a http://github.com.
6. Fare clic su **OK** > **Chiudi** negli avvisi di sicurezza.

   Verrà visualizzata la home page di GitHub.

7. Passare a http://www.msn.com.

   Si verrà bloccati dal firewall.

A questo punto si è verificato che le regole del firewall funzionano:

- È possibile passare al nome di dominio completo consentito ma non agli altri.
- È possibile risolvere i nomi DNS con il server DNS esterno configurato.

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile conservare le risorse del firewall per l'esercitazione successiva oppure, se non è più necessario, eliminare il gruppo di risorse **Test-FW-RG** per eliminare tutte le risorse correlate al firewall.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: Monitorare i log di Firewall di Azure](./tutorial-diagnostics.md)

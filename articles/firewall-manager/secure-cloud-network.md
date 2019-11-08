---
title: "Esercitazione: Usare l'anteprima di Gestione firewall di Azure per proteggere la rete cloud tramite il portale di Azure"
description: In questa esercitazione viene illustrato come proteggere la rete cloud con Gestione firewall di Azure usando il portale di Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 10/27/2019
ms.author: victorh
ms.openlocfilehash: d2ebfd6003c0bc2b47636be1e38f47e554cc6988
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73510035"
---
# <a name="tutorial-secure-your-cloud-network-with-azure-firewall-manager-preview-using-the-azure-portal"></a>Esercitazione: Proteggere la rete cloud con l'anteprima di Gestione firewall di Azure usando il portale di Azure

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Usando l'anteprima di Gestione firewall di Azure, è possibile creare hub protetti per proteggere il traffico di rete cloud destinato a indirizzi IP privati, a PaaS di Azure e a Internet. Il routing del traffico verso il firewall è automatizzato, pertanto non è necessario creare route definite dall'utente.

![proteggere la rete cloud](media/secure-cloud-network/secure-cloud-network.png)

## <a name="prerequisites"></a>Prerequisiti

> [!IMPORTANT]
> L'anteprima di Gestione firewall di Azure deve essere abilitata in modo esplicito con il comando di PowerShell `Register-AzProviderFeature`.

Dal prompt dei comandi di PowerShell eseguire questi comandi:

```azure-powershell
connect-azaccount
Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
```
Sono necessari fino a 30 minuti per completare la registrazione della funzionalità. Eseguire questo comando per controllare lo stato della registrazione:

`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`

## <a name="create-a-hub-and-spoke-architecture"></a>Creare un'architettura hub-spoke

Prima di tutto, creare una rete virtuale spoke in cui poter collocare i server.

### <a name="create-a-spoke-vnet-and-subnets"></a>Creare una rete virtuale spoke e le subnet

1. Dalla home page del portale di Azure selezionare **Crea una risorsa**.
2. In **Rete** selezionare **Rete virtuale**.
4. Per **Nome**, digitare **Spoke-01**.
5. In **Spazio degli indirizzi** immettere **10.0.0.0/16**.
6. In **Sottoscrizione** selezionare la propria sottoscrizione.
7. Per **Gruppo di risorse**, selezionare **Crea nuovo**, digitare **FW-Manager** come nome e selezionare **OK**.
8. In **Località** selezionare **(USA) Stati Uniti orientali**.
9. In **Subnet**, per **Nome**, digitare **Workload-SN**.
10. In **Intervallo indirizzi** immettere **10.0.1.0/24**.
11. Accettare le altre impostazioni predefinite e quindi selezionare **Crea**.

Creare quindi una subnet per un jump server.

1. Nella home page del portale di Azure selezionare **Gruppi di risorse** > **FW-Manager**.
2. Selezionare la rete virtuale **Spoke-01**.
3. Selezionare **Subnet** >  **+Subnet**.
4. Per **Nome**, digitare **Jump-SN**.
5. In **Intervallo indirizzi** immettere **10.0.2.0/24**.
6. Selezionare **OK**.

### <a name="create-the-secured-virtual-hub"></a>Creare l'hub virtuale protetto

Creare l'hub virtuale protetto usando Gestione firewall.

1. Nella home page del portale di Azure selezionare **Tutti i servizi**.
2. Nella casella di ricerca digitare **Gestione firewall**, quindi selezionare **Gestione firewall**.
3. Nella pagina **Gestione firewall** selezionare **Crea un hub virtuale protetto**.
4. Nella pagina **Crea nuovo hub virtuale protetto** selezionare la propria sottoscrizione e il gruppo di risorse **FW-Manager**.
5. Per **Nome hub virtuale protetto**, digitare **Hub-01**.
6. In **Località** selezionare **Stati Uniti orientali**.
7. Per **Spazio indirizzi dell'hub**, digitare **10.1.0.0/16**.
8. Per il nome della nuova rete WAN virtuale, digitare **vwan-01**.
9. Lasciare deselezionata la casella di controllo **Includere il gateway VPN per abilitare i partner di sicurezza affidabili**.
10. Selezionare **Avanti: Firewall di Azure**.
11. Accettare l'impostazione predefinita **Firewall di Azure** **Abilitato** e quindi selezionare **Avanti: Partner di sicurezza affidabile**.
12. Accettare l'impostazione predefinita **Partner di sicurezza affidabile** **Disabilitato** e selezionare **Avanti: Rivedi e crea**.
13. Selezionare **Create** (Crea). La distribuzione richiederà circa 30 minuti.

### <a name="connect-the-hub-and-spoke-vnets"></a>Connettere le reti virtuali hub e spoke

Ora è possibile eseguire il peering tra le reti virtuali hub e spoke.

1. Selezionare il gruppo di risorse **FW-Manager** e quindi selezionare la rete WAN virtuale **vwan-01**.
2. In **Connettività** selezionare **Connessioni rete virtuale**.
3. Selezionare **Aggiungi connessione**.
4. Per **Nome connessione**, digitare **hub-spoke**.
5. Per **Hub**, selezionare **Hub-01**.
6. Per **Rete virtuale**, selezionare **Spoke-01**.
7. Selezionare **OK**.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Creare un criterio firewall e proteggere l'hub

Un criterio firewall definisce raccolte di regole per indirizzare il traffico su uno o più hub virtuali protetti. Sarà perciò necessario creare il criterio firewall e quindi proteggere l'hub.

1. In Gestione firewall selezionare **Crea un criterio firewall di Azure**.
2. Selezionare la propria sottoscrizione e quindi il gruppo di risorse **FW-Manager**.
3. In **Dettagli criteri**, per **Nome** digitare **Policy-01** e per **Area** selezionare **Stati Uniti orientali**.
4. Selezionare **Avanti: Regole**.
5. Nella scheda **Regole** selezionare **Aggiungi una raccolta regole**.
6. Nella pagina **Aggiungi una raccolta regole** digitare **RC-01** in **Nome**.
7. Per **Tipo di raccolta regole**, selezionare **Applicazione**.
8. In **Priorità** digitare **100**.
9. Verificare che **Azione raccolta regole** sia **Consenti**.
10. Come **Nome** della regola, digitare **Allow-msft**.
11. Per **Indirizzo di origine**, digitare **\*** .
12. Per **Protocollo**, digitare **http,https**.
13. Verificare che **Tipo di destinazione sia impostato su **FQDN**.
14. Per **Destinazione**, digitare **\*.microsoft.com**.
15. Selezionare **Aggiungi**.
16. Selezionare **Avanti: Hub virtuali protetti**.
17. Nella scheda **Hub virtuali protetti** selezionare **Hub-01**.
19. Selezionare **Rivedi e crea**.
20. Selezionare **Create** (Crea).

Il completamento di questa operazione può richiedere circa cinque minuti.

## <a name="route-traffic-to-your-hub"></a>Instradare il traffico all'hub

A questo punto è necessario assicurarsi che il traffico di rete venga instradato attraverso il firewall.

1. In Gestione firewall selezionare **Hub virtuali protetti**.
2. Selezionare **Hub-01**.
3. In **Impostazioni** selezionare **Impostazioni route**.
4. In **Traffico Internet**, **Traffico dalle reti virtuali**, selezionare **Invia tramite Firewall di Azure**.
5. In **Traffico privato di Azure**, **Traffico alle reti virtuali**, selezionare **Invia tramite Firewall di Azure**.
6. Selezionare **Modifica prefisso/i indirizzi IP**.
7. Selezionare **Aggiungi un prefisso di indirizzo IP**.
8. Digitare **10.0.1.0/24** come indirizzo della subnet del carico di lavoro e selezionare **Salva**.
9. In **Impostazioni** selezionare **Connessioni**.
10. Selezionare la connessione **hub-spoke**, quindi **Proteggi traffico Internet** e infine **OK**.


## <a name="test-your-firewall"></a>Testare il firewall

Per testare le regole del firewall, è necessario distribuire un paio di server. Si distribuirà Workload-Srv nella subnet Workload-SN per testare le regole del firewall e quindi Jump-Srv in modo da poter usare Desktop remoto per connettersi da Internet e quindi connettersi a Workload-Srv.

### <a name="deploy-the-servers"></a>Distribuire i server

1. Nel portale di Azure fare clic su **Crea una risorsa**.
2. Selezionare **Windows Server 2016 Datacenter** nell'elenco **Più comuni**.
3. Immettere i valori seguenti per la macchina virtuale:

   |Impostazione  |Valore  |
   |---------|---------|
   |Resource group     |**FW-Manager**|
   |Nome macchina virtuale     |**Jump-Srv**|
   |Region     |**(USA) Stati Uniti orientali**|
   |Nome utente amministratore     |**azureuser**|
   |Password     |**Azure123456!**|

4. In **Regole porta in ingresso** selezionare **Consenti porte selezionate** per **Porte in ingresso pubbliche**.
5. In **Selezionare le porte in ingresso** selezionare **RDP (3389)** .

6. Accettare tutte le altre impostazioni predefinite e selezionare **Avanti: Dischi**.
7. Accettare tutte le impostazioni predefinite del disco e selezionare **Avanti: Rete**.
8. Assicurarsi che **Spoke-01** sia selezionata come rete virtuale e che la subnet sia **Jump-SN**.
9. Per **Indirizzo IP pubblico**, accettare il nome predefinito del nuovo indirizzo IP pubblico (Jump-Srv-ip).
11. Accettare tutte le altre impostazioni predefinite e selezionare **Avanti: Gestione**.
12. Selezionare **Disattivato** per disabilitare la diagnostica di avvio. Accettare tutte le altre impostazioni predefinite e selezionare **Rivedi e crea**.
13. Verificare le impostazioni nella pagina di riepilogo e quindi selezionare **Crea**.

Usare le informazioni nella tabella seguente per configurare un'altra macchina virtuale denominata **Workload-Srv**. Il resto della configurazione è uguale a quella della macchina virtuale Srv-Jump.

|Impostazione  |Valore  |
|---------|---------|
|Subnet|**Workload-SN**|
|IP pubblico|**Nessuno**|
|Porte in ingresso pubbliche|**Nessuno**|

### <a name="add-a-route-table-and-default-route"></a>Aggiungere una tabella di route e una route predefinita

Per consentire una connessione Internet a Jump-Srv, è necessario creare una tabella di route e una route gateway predefinita a Internet dalla subnet **Jump-SN**.

1. Nel portale di Azure fare clic su **Crea una risorsa**.
2. Digitare **tabella di route** nella casella di ricerca e quindi selezionare **Tabella di route**.
3. Selezionare **Create** (Crea).
4. Digitare **RT-01** in **Nome**.
5. Selezionare la propria sottoscrizione, **FW-Manager** come gruppo di risorse e **(USA) Stati Uniti orientali** come area.
6. Selezionare **Create** (Crea).
7. Al termine della distribuzione, selezionare la tabella di route **RT-01**.
8. Selezionare **Route**, quindi **Aggiungi**.
9. Digitare **jump-to-inet** in **Nome route**.
10. Digitare **0.0.0.0/0** in **Prefisso indirizzo**.
11. Selezionare **Internet** per **Tipo hop successivo**.
12. Selezionare **OK**.
13. Al termine della distribuzione, selezionare **Subnet** e quindi **Associa**.
14. Selezionare **Spoke-01** per **Rete virtuale**.
15. Selezionare **Jump-SN** per **Subnet**.
16. Selezionare **OK**.

### <a name="test-the-rules"></a>Testare le regole

A questo punto testare le regole del firewall per verificare che tutto funzioni come previsto.

1. Nel portale di Azure rivedere le impostazioni di rete per la macchina virtuale **Workload-Srv** e annotare l'indirizzo IP privato.
2. Connettere una sessione di Desktop remoto alla macchina virtuale **Jump-Srv** e accedere. Da qui aprire una connessione Desktop remoto all'indirizzo IP privato di **Workload-Srv**.

3. Aprire Internet Explorer e passare a https://www.microsoft.com.
4. Selezionare **OK** > **Close** negli avvisi di sicurezza di Internet Explorer.

   Verrà visualizzata la home page Microsoft.

5. Passare a https://www.google.com.

   Si verrà bloccati dal firewall.

A questo punto si è verificato che le regole del firewall funzionano:

* È possibile passare al nome di dominio completo consentito ma non agli altri.



## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Vedere le informazioni sui partner di sicurezza affidabili](trusted-security-partners.md)

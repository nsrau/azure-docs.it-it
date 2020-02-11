---
title: Integrazione con il servizio Collegamento privato di Azure
description: Informazioni su come integrare Azure Key Vault con il servizio Collegamento privato di Azure
author: msmbaldwin
ms.author: mbaldwin
ms.date: 01/28/2020
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: e058e643f4c37336f09b43c41cd09aa361a23d15
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76908635"
---
# <a name="integrate-key-vault-with-azure-private-link-preview"></a>Integrare Key Vault con Collegamento privato di Azure (anteprima)

Il servizio Collegamento privato di Azure consente di accedere ai servizi di Azure, ad esempio Azure Key Vault, Archiviazione di Azure e Azure Cosmos DB, e ai servizi di clienti/partner ospitati in Azure tramite un endpoint privato nella rete virtuale.

Un endpoint privato di Azure è un'interfaccia di rete che connette privatamente e in modo sicuro a un servizio basato su Collegamento privato di Azure. L'endpoint privato usa un indirizzo IP privato della rete virtuale, introducendo efficacemente il servizio nella rete virtuale. Tutto il traffico verso il servizio può essere instradato tramite l'endpoint privato, quindi non sono necessari gateway, dispositivi NAT, ExpressRoute o connessioni VPN oppure indirizzi IP pubblici. Il traffico tra la rete virtuale e il servizio attraversa la rete backbone Microsoft, impedendone l'esposizione alla rete Internet pubblica. È possibile connettersi a un'istanza di una risorsa di Azure, garantendo il massimo livello di granularità nel controllo di accesso.

Per altre informazioni, vedere [Che cos'è Collegamento privato di Azure? (Anteprima)](../private-link/private-link-overview.md).

## <a name="prerequisites"></a>Prerequisites

Per integrare un insieme di credenziali delle chiavi con Collegamento privato di Azure (anteprima), è necessario quanto segue:

- Un insieme di credenziali delle chiavi.
- Una rete virtuale di Azure.
- Una subnet nella rete virtuale.
- Autorizzazioni di proprietario o collaboratore per l'insieme di credenziali delle chiavi e la rete virtuale.

L'endpoint privato e la rete virtuale devono trovarsi nella stessa area. Quando si seleziona un'area per l'endpoint privato tramite il portale, verranno automaticamente filtrate solo le reti virtuali presenti in tale area. L'insieme di credenziali delle chiavi può trovarsi in un'area diversa.

L'endpoint privato usa un indirizzo IP privato nella rete virtuale.

## <a name="establish-a-private-link-connection-to-key-vault"></a>Stabilire una connessione di collegamento privato all'insieme di credenziali delle chiavi

Creare prima di tutto una rete virtuale seguendo la procedura descritta nell'articolo [Creare una rete virtuale usando il portale di Azure](../virtual-network/quick-create-portal.md).

È quindi possibile creare un nuovo insieme di credenziali delle chiavi o stabilire una connessione di collegamento privato a un insieme di credenziali delle chiavi esistente.

### <a name="create-a-new-key-vault-and-establish-a-private-link-connection"></a>Creare un nuovo insieme di credenziali delle chiavi e stabilire una connessione di collegamento privato

È possibile creare un nuovo insieme di credenziali delle chiavi seguendo la procedura descritta nell'articolo [Impostare e recuperare un segreto da Azure Key Vault usando il portale di Azure](quick-create-portal.md).

Dopo aver configurato le informazioni di base dell'insieme di credenziali delle chiavi, selezionare la scheda Rete e seguire questa procedura:

1. Selezionare il pulsante di opzione Endpoint privato (anteprima) nella scheda Rete.
1. Fare clic sul pulsante "+ Aggiungi" per aggiungere un endpoint privato.

    ![Immagine](./media/private-link-service-1.png)
 
1. Nel campo "Posizione" del pannello Crea endpoint privato selezionare l'area in cui si trova la rete virtuale. 
1. Nel campo "Nome" creare un nome descrittivo che consentirà di identificare questo endpoint privato. 
1. Selezionare la rete virtuale e la subnet in cui si vuole creare questo endpoint privato nel menu a discesa. 
1. Lasciare invariata l'opzione "Integra con la zona DNS privato".  
1. Selezionare "Ok".

    ![Immagine](./media/private-link-service-2.png)
 
Sarà ora possibile visualizzare l'endpoint privato configurato. È ora possibile eliminare e modificare questo endpoint privato. Selezionare il pulsante "Rivedi e crea" e creare l'insieme di credenziali delle chiavi. Il completamento della distribuzione richiederà 5-10 minuti. 

### <a name="establish-a-private-link-connection-to-an-existing-key-vault"></a>Stabilire una connessione di collegamento privato a un insieme di credenziali delle chiavi esistente

Se si dispone già di un insieme di credenziali delle chiavi, è possibile creare una connessione di collegamento privato attenendosi alla procedura seguente:

1. Accedere al portale di Azure. 
1. Nella barra di ricerca digitare "key vaults".
1. Selezionare nell'elenco l'insieme di credenziali delle chiavi in cui si vuole aggiungere un endpoint privato.
1. Selezionare la scheda "Rete" in Impostazioni.
1. Selezionare la scheda Connessioni endpoint privato (anteprima) nella parte superiore della pagina.
1. Selezionare il pulsante "+ Endpoint privato" nella parte superiore della pagina.

    ![Immagine](./media/private-link-service-3.png) ![Immagine](./media/private-link-service-4.png)

È possibile scegliere di creare un endpoint privato per qualsiasi risorsa di Azure usando questo pannello. È possibile usare i menu a discesa per selezionare un tipo di risorsa e selezionare una risorsa nella directory oppure è possibile connettersi a qualsiasi risorsa di Azure usando un ID risorsa. Lasciare invariata l'opzione "Integra con la zona DNS privato".  

![Immagine](./media/private-link-service-3.png)
![Immagine](./media/private-link-service-4.png)

## <a name="manage-private-link-connection"></a>Gestire una connessione di collegamento privato

Quando si crea un endpoint privato, la connessione deve essere approvata. Se la risorsa per cui si sta creando un endpoint privato si trova nella directory, sarà possibile approvare la richiesta di connessione purché si disponga di autorizzazioni sufficienti. Se ci si connette a una risorsa di Azure in un'altra directory, è necessario attendere che il proprietario della risorsa approvi la richiesta di connessione.

Sono disponibili quattro stati di provisioning:

| Azione del provider del servizio | Stato dell'endpoint privato del consumer del servizio | Descrizione |
|--|--|--|
| nessuno | In sospeso | La connessione viene creata manualmente ed è in attesa di approvazione dal proprietario della risorsa di collegamento privato. |
| Approvazione | Approved | La connessione è stata approvata automaticamente o manualmente ed è pronta per essere usata. |
| Rifiuto | Rifiutato | La connessione è stata rifiutata dal proprietario della risorsa di collegamento privato. |
| Rimuovere | Disconnesso | La connessione è stata rimossa dal proprietario della risorsa di collegamento privato, l'endpoint privato diventa informativo e deve essere eliminato per la pulizia. |
 
###  <a name="how-to-manage-a-private-endpoint-connection-to-key-vault"></a>Come gestire una connessione di endpoint privato a un insieme di credenziali delle chiavi

1. Accedere al portale di Azure.
1. Nella barra di ricerca digitare "key vaults".
1. Selezionare l'insieme di credenziali delle chiavi che si vuole gestire.
1. Selezionare la scheda "Rete".
1. Se sono presenti connessioni in sospeso, verrà visualizzata una connessione elencata con "In sospeso" nello stato di provisioning. 
1. Selezionare l'endpoint privato che si vuole approvare.
1. Selezionare il pulsante Approva.
1. Se sono presenti connessioni di endpoint privato che si vuole rifiutare, sia che si tratti di una richiesta in sospeso o di una connessione esistente, selezionare la connessione e fare clic sul pulsante "Rifiuta".

    ![Immagine](./media/private-link-service-7.png)

## <a name="validate-that-the-private-link-connection-works"></a>Verificare il funzionamento della connessione di collegamento privato

È necessario verificare che le risorse all'interno della stessa subnet della risorsa endpoint privato si connettano all'insieme di credenziali delle chiavi tramite un indirizzo IP privato e che dispongano dell'integrazione con la zona DNS privato corretta.

Creare prima di tutto una macchina virtuale seguendo la procedura descritta nell'articolo [Creare una macchina virtuale di Windows nel portale di Azure](../virtual-machines/windows/quick-create-portal.md).

Nella scheda "Rete":

1. Specificare una rete virtuale e una subnet. È possibile creare una nuova macchina virtuale o selezionarne una esistente. Se si seleziona una macchina virtuale esistente, assicurarsi che l'area corrisponda.
1. Specificare una risorsa IP pubblico.
1. In "Gruppo di sicurezza di rete della scheda di interfaccia di rete" selezionare "Nessuno".
1. In "Bilanciamento del carico" selezionare "No".

Aprire la riga di comando ed eseguire il comando seguente:

```console
nslookup <your-key-vault-name>.vault.azure.net
```

Se si esegue il comando ns lookup per risolvere l'indirizzo IP di un insieme di credenziali delle chiavi su un endpoint pubblico, verrà visualizzato un risultato simile al seguente:

```console
c:\ >nslookup <your-key-vault-name>.vault.azure.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
```

Se si esegue il comando ns lookup per risolvere l'indirizzo IP di un insieme di credenziali delle chiavi su un endpoint privato, verrà visualizzato un risultato simile al seguente:

```console
c:\ >nslookup your_vault_name.vault.azure.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
          <your-key-vault-name>.privatelink.vaultcore.azure.net
```

## <a name="limitations-and-design-considerations"></a>Limitazioni e considerazioni di progettazione

**Pricing** (Prezzi): per informazioni sui prezzi, vedere [Prezzi di Collegamento privato di Azure (Anteprima)](https://azure.microsoft.com/pricing/details/private-link/).

**Limitazioni**:  l'endpoint privato per Azure Key Vault è in anteprima pubblica. Questa funzionalità è disponibile in tutte le aree pubbliche di Azure.

**Numero massimo di endpoint privati per Key Vault**: 64.

**Numero massimo di insiemi di credenziali delle chiavi con endpoint privati per ogni sottoscrizione**: 64.

Per altre informazioni, vedere [Servizio Collegamento privato di Azure: Limitazioni](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Collegamento privato di Azure (anteprima)](../private-link/private-link-service-overview.md)
- Altre informazioni su [Azure Key Vault](key-vault-overview.md)

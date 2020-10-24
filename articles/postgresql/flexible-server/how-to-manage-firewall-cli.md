---
title: Gestire le regole del firewall-interfaccia della riga di comando di Azure-database di Azure per PostgreSQL-server flessibile
description: Creare e gestire le regole del firewall per database di Azure per PostgreSQL-server flessibile usando la riga di comando di Azure.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 09/22/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 36249694c5a4de8a738853892f827c6d9e1e4aff
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489473"
---
# <a name="create-and-manage-azure-database-for-postgresql---flexible-server-firewall-rules-using-the-azure-cli"></a>Creare e gestire database di Azure per PostgreSQL: regole flessibili del firewall del server usando l'interfaccia della riga di comando di Azure

> [!IMPORTANT]
> Il server flessibile di Database di Azure per PostgreSQL è disponibile in anteprima

Database di Azure per PostgreSQL: il server flessibile supporta due tipi di metodi di connettività di rete che si escludono a vicenda per la connessione al server flessibile. Le due opzioni sono:

* Accesso pubblico (indirizzi IP consentiti)
* Accesso privato (integrazione rete virtuale)

Questo articolo illustra la creazione di un server PostgreSQL con **accesso pubblico (indirizzi IP consentiti)** usando l'interfaccia della riga di comando di Azure e fornisce una panoramica sui comandi dell'interfaccia della riga di comando di Azure che è possibile usare per creare, aggiornare, eliminare, elencare e visualizzare le regole del firewall dopo la creazione del server. Con l' *accesso pubblico (indirizzi IP consentiti)*, le connessioni al server PostgreSQL sono limitate solo agli indirizzi IP consentiti. Gli indirizzi IP client devono essere consentiti nelle regole del firewall. Per altre informazioni, vedere [accesso pubblico (indirizzi IP consentiti)](./concepts-networking.md#public-access-allowed-ip-addresses). Le regole del firewall possono essere definite al momento della creazione del server (scelta consigliata), ma possono essere aggiunte anche in seguito.

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

[Azure Cloud Shell](../../cloud-shell/overview.md) è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account.

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile aprire Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/bash](https://shell.azure.com/bash). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere **INVIO** per eseguirli.

Se si preferisce installare e usare l'interfaccia della riga di comando in locale, per questo argomento di avvio rapido è necessaria l'interfaccia della riga di comando di Azure versione 2.0 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Prerequisiti

È necessario accedere all'account con il comando [az login](/cli/azure/reference-index#az-login). Prendere nota della proprietà **ID** , che fa riferimento all' **ID sottoscrizione** per l'account Azure.

```azurecli-interactive
az login
```

Selezionare la sottoscrizione specifica nell'account tramite il comando [az account set](/cli/azure/account#az-account-set). Prendere nota del valore **ID** del comando **AZ login** output da usare come valore per l'argomento **Subscription** nel comando. Se si possiedono più sottoscrizioni, scegliere quella appropriata in cui verrà fatturata la risorsa. Per ottenere tutte le sottoscrizioni, usare [az account list](/cli/azure/account#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-firewall-rule-during-flexible-server-create-using-azure-cli"></a>Creare una regola del firewall durante la creazione di server flessibili con l'interfaccia della riga

È possibile utilizzare il `az postgres flexible-server --public access` comando per creare il server flessibile con *accesso pubblico (indirizzi IP consentiti)* e configurare le regole del firewall durante la creazione di un server flessibile. È possibile usare l'opzione **--Public-Access** per fornire gli indirizzi IP consentiti che saranno in grado di connettersi al server. È possibile specificare un singolo o un intervallo di indirizzi IP da includere nell'elenco di indirizzi IP consentiti. L'intervallo di indirizzi IP deve essere separato da trattini e non contiene spazi. Sono disponibili diverse opzioni per creare un server flessibile usando l'interfaccia della riga di comando, come illustrato nell'esempio riportato di seguito.

Vedere la documentazione di riferimento dell'interfaccia della riga di comando Azure <!--FIXME --> per avere l'elenco completo dei parametri configurabili dell'interfaccia della riga di comando. Ad esempio, nei comandi seguenti è possibile specificare facoltativamente il gruppo di risorse.

- Creare un server flessibile con accesso pubblico e aggiungere l'indirizzo IP del client per avere accesso al server
    ```azurecli-interactive
    az postgres flexible-server create --public-access <my_client_ip>
    ```
- Creare un server flessibile con accesso pubblico e aggiungere l'intervallo di indirizzi IP per accedere a questo server

    ```azurecli-interactive
    az postgres flexible-server create --public-access <start_ip_address-end_ip_address>
    ```
- Creare un server flessibile con accesso pubblico e consentire alle applicazioni degli indirizzi IP di Azure di connettersi al server flessibile
    ```azurecli-interactive
    az postgres flexible-server create --public-access 0.0.0.0
    ```
    > [!IMPORTANT]
    > Questa opzione consente di configurare il firewall in modo da consentire l'accesso pubblico dai servizi e dalle risorse di Azure in Azure a questo server, incluse le connessioni dalle sottoscrizioni di altri clienti. Quando si seleziona questa opzione, verificare che l'account di accesso e le autorizzazioni utente limitino l'accesso ai soli utenti autorizzati.
    >
- - Creare un server flessibile con accesso pubblico e consentire tutti gli indirizzi IP
    ```azurecli-interactive
    az postgres flexible-server create --public-access all
    ```
    >[!Note]
    > Il comando precedente creerà una regola del firewall con indirizzo IP iniziale = 0.0.0.0, indirizzo IP finale = 255.255.255.255 e nessun indirizzo IP verrà bloccato. Qualsiasi host in Internet può accedere a questo server. Si consiglia vivamente di utilizzare questa regola solo temporaneamente e solo nei server di prova che non contengono dati sensibili.
- Creare un server flessibile con accesso pubblico e senza indirizzo IP
    ```azurecli-interactive
    az postgres flexible-server create --public-access none
    ```
    >[!Note]
    > non è consigliabile creare un server senza alcuna regola del firewall. Se non si aggiungono regole firewall, nessun client sarà in grado di connettersi al server.
## <a name="create-and-manage-firewall-rule-after-server-create"></a>Creare e gestire la regola del firewall dopo la creazione del server
Il comando **AZ Postgres flexible-server firewall-rule** viene usato dall'interfaccia della riga di comando di Azure per creare, eliminare, elencare, visualizzare e aggiornare le regole del firewall.

Comandi:
- **Crea**: creare una regola del firewall del server flessibile.
- **elenco**: elencare le regole flessibili del firewall del server.
- **aggiornamento**: aggiornare una regola del firewall del server flessibile.
- **Mostra**: Mostra i dettagli di una regola del firewall del server flessibile.
- **Elimina**: eliminare una regola del firewall flessibile del server.

Vedere la documentazione di riferimento dell'interfaccia della riga di comando Azure <!--FIXME --> per avere l'elenco completo dei parametri configurabili dell'interfaccia della riga di comando. Ad esempio, nei comandi seguenti è possibile specificare facoltativamente il gruppo di risorse.

### <a name="create-a-firewall-rule"></a>Creare una regola del firewall
Usare il `az postgres flexible-server firewall-rule create` comando per creare una nuova regola del firewall nel server.
Per consentire l'accesso a un intervallo di indirizzi IP, fornire l'indirizzo IP iniziale e l'indirizzo IP finale, come in questo esempio.
```azurecli-interactive
az postgres flexible-server firewall-rule create --name mydemoserver --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Per consentire l'accesso per un singolo indirizzo IP, è sufficiente specificare un singolo indirizzo IP, come in questo esempio.
```azurecli-interactive
az postgres flexible-server firewall-rule create --name mydemoserver --start-ip-address 1.1.1.1
```

Per consentire alle applicazioni degli indirizzi IP di Azure di connettersi al server flessibile, fornire l'indirizzo IP 0.0.0.0 come IP iniziale, come in questo esempio.
```azurecli-interactive
az postgres flexible-server firewall-rule create --name mydemoserver --start-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Questa opzione consente di configurare il firewall in modo da consentire l'accesso pubblico dai servizi e dalle risorse di Azure in Azure a questo server, incluse le connessioni dalle sottoscrizioni di altri clienti. Quando si seleziona questa opzione, verificare che l'account di accesso e le autorizzazioni utente limitino l'accesso ai soli utenti autorizzati.
> 
Al termine dell'operazione, l'output di ogni comando create elenca i dettagli della regola del firewall creata, in formato JSON (per impostazione predefinita). Se si verifica un errore, l'output visualizza invece il testo di un messaggio di errore.

### <a name="list-firewall-rules"></a>Elencare le regole del firewall 
Usare il `az postgres flexible-server firewall-rule list` comando per elencare le regole del firewall del server esistenti nel server. Si noti che l'attributo nome server è specificato nell'opzione **--Name** . 
```azurecli-interactive
az postgres flexible-server firewall-rule list --name mydemoserver
```
L'output elenca le eventuali regole presenti in formato JSON (per impostazione predefinita). È possibile usare l'opzione--output Table * * per restituire i risultati in un formato di tabella più leggibile.
```azurecli-interactive
az postgres flexible-server firewall-rule list --name mydemoserver --output table
```

### <a name="update-a-firewall-rule"></a>Aggiornare una regola del firewall
Usare il `az postgres flexible-server firewall-rule update` comando per aggiornare una regola del firewall esistente nel server. Specificare il nome della regola del firewall esistente come input, nonché gli attributi indirizzo IP iniziale e indirizzo IP finale da aggiornare.
```azurecli-interactive
az postgres flexible-server firewall-rule update --name mydemoserver --rule-name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Al termine dell'operazione, l'output del comando elenca i dettagli della regola del firewall aggiornata, in formato JSON (per impostazione predefinita). Se si verifica un errore, l'output visualizza invece il testo di un messaggio di errore.

> [!NOTE]
> Se la regola del firewall non esiste, viene creata dal comando di aggiornamento.
### <a name="show-firewall-rule-details"></a>Visualizzare i dettagli di una regola del firewall
Usare il `az postgres flexible-server firewall-rule show` comando per visualizzare i dettagli della regola del firewall esistente dal server. Specificare il nome della regola del firewall esistente come input.
```azurecli-interactive
az postgres flexible-server firewall-rule show --name mydemoserver --rule-name FirewallRule1
```
Al termine dell'operazione, l'output del comando elenca i dettagli della regola del firewall specificata, in formato JSON (per impostazione predefinita). Se si verifica un errore, l'output visualizza invece il testo di un messaggio di errore.

### <a name="delete-a-firewall-rule"></a>Eliminare una regola del firewall
Usare il `az postgres flexible-server firewall-rule delete` comando per eliminare una regola del firewall esistente dal server. Specificare il nome della regola del firewall esistente.
```azurecli-interactive
az postgres flexible-server firewall-rule delete --name mydemoserver --rule-name FirewallRule1
```
Al completamento dell'operazione non verrà visualizzato alcun output. In caso di errore, viene visualizzato il testo di un messaggio di errore.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulla [rete in database di Azure per PostgreSQL-server flessibile](./concepts-networking.md)
- Altre informazioni su [database di Azure per PostgreSQL-regole flessibili del firewall del server](./concepts-networking.md#public-access-allowed-ip-addresses)
- [Creare e gestire database di Azure per PostgreSQL: regole flessibili del firewall del server usando il portale di Azure](./how-to-manage-firewall-portal.md).

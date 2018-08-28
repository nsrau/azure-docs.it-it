---
title: Creare e gestire gli endpoint e le regole di servizio di rete virtuale per Database di Azure per MySQL tramite l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Questo articolo descrive come creare e gestire gli endpoint e le regole di servizio di rete virtuale per Database di Azure per MySQL tramite l'interfaccia della riga di comando di Azure.
services: mysql
author: mbolz
ms.author: mbolz
manager: jhubbard
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: article
ms.date: 08/15/2018
ms.openlocfilehash: dd856cda58bc88c8c3d928ac41366caaa15faac9
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2018
ms.locfileid: "42144294"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-using-azure-cli"></a>Creare e gestire gli endpoint di servizio di rete virtuale per Database di Azure per MySQL tramite l'interfaccia della riga di comando di Azure
Gli endpoint e le regole dei servizi di rete virtuale estendono lo spazio di indirizzi privato di una rete virtuale a un server di Database di Azure per MySQL. Usando pratici comandi dell'interfaccia della riga di comando di Azure è possibile creare, aggiornare, eliminare, elencare e visualizzare gli endpoint e le regole di servizio rete virtuale per gestire il server. Per una panoramica degli endpoint di servizio di rete virtuale per Database di Azure per MySQL, incluse le limitazioni, vedere [Usare gli endpoint e le regole di servizio di rete virtuale per Database di Azure per MySQL](concepts-data-access-and-security-vnet.md). Gli endpoint di servizio di rete virtuale sono disponibili in tutte le aree supportate per Database di Azure per MySQL.

## <a name="prerequisites"></a>Prerequisiti
Per proseguire con questa guida, si richiedono:
- Installare l'utilità della riga di comando dell'[interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) o usare Azure Cloud Shell nel browser.
- Un'istanza di [Database di Azure per il server e il database MySQL](quickstart-create-mysql-server-database-using-azure-cli.md).

> [!NOTE]
> Gli endpoint di servizio di rete virtuale sono supportati solo per i server per utilizzo generico e ottimizzati per la memoria.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-mysql"></a>Configurare gli endpoint di servizio di rete virtuale per Database di Azure per MySQL
Per configurare le reti virtuali vengono usati i comandi [az network vnet](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest).

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Per vedere la versione installata, eseguire il comando `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 

Se si esegue l'interfaccia della riga di comando in locale, è necessario accedere al proprio account con il comando [az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest). Si noti la proprietà **id** dell'output del comando per il nome della sottoscrizione corrispondente.
```azurecli-interactive
az login
```

Se si possiedono più sottoscrizioni, scegliere quella appropriata in cui verrà fatturata la risorsa. Selezionare l'ID sottoscrizione specifico sotto l'account tramite il comando [az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set). Sostituire la proprietà **id** dell'output **az login** per la sottoscrizione nel segnaposto dell'ID sottoscrizione.

- L'account deve avere le autorizzazioni necessarie per la creazione di una rete virtuale e di un endpoint di servizio.

Gli endpoint di servizio possono essere configurati sulle reti virtuali, in modo indipendente, da un utente con accesso in scrittura alla rete virtuale.

Per associare le risorse dei servizi di Azure a una rete virtuale, l'utente deve avere l'autorizzazione "Microsoft.Network/JoinServicetoaSubnet" per le subnet da aggiungere. Per impostazione predefinita, questa autorizzazione è inclusa nei ruoli di amministratore del servizio predefiniti e può essere modificata creando ruoli personalizzati.

Altre informazioni sui [ruoli predefiniti](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) e sull'assegnazione di autorizzazioni specifiche ai [ruoli personalizzati](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

Le reti virtuali e le risorse dei servizi di Azure possono essere nella stessa sottoscrizione o in sottoscrizioni diverse. Se la rete virtuale e le risorse dei servizi di Azure si trovano in sottoscrizioni diverse, le risorse devono trovarsi nello stesso tenant di Active Directory (AD).

> [!IMPORTANT]
> Prima di eseguire lo script di esempio riportato di seguito o configurare gli endpoint di servizio è consigliabile leggere questo articolo in cui sono riportate considerazioni e istruzioni di configurazione per gli endpoint di servizio. **Endpoint di servizio di rete virtuale:** un [endpoint di servizio di rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md) è una subnet in cui i valori delle proprietà includono uno o più nomi formali di tipi di servizi Azure. Gli endpoint dei servizi di rete virtuale usano il nome del tipo di servizio **Microsoft.Sql**, che fa riferimento al servizio di Azure denominato Database SQL. Questo tag di servizio si applica al database SQL di Azure e ai servizi di Database di Azure per PostgreSQL e MySQL. È importante tenere presente che, quando si applica il tag di servizio **Microsoft.Sql** a un endpoint di servizio di rete virtuale, viene configurato il traffico dell'endpoint per tutti i server di Database SQL di Azure, Database di Azure per PostgreSQL e Database di Azure per MySQL nella subnet. 
> 

### <a name="sample-script-to-create-an-azure-database-for-mysql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Script di esempio per creare un'istanza di Database di Azure per MySQL, una rete virtuale e un endpoint di servizio di rete virtuale e per proteggere il server nella subnet con una regola di rete virtuale
In questo script di esempio modificare le righe evidenziate per personalizzare nome utente e password amministratore. Sostituire l'ID sottoscrizione usato nel comando `az account set --subscription` con il proprio ID sottoscrizione.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MySQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione
Dopo l'esecuzione dello script di esempio, è possibile usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse ad esso associate.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]

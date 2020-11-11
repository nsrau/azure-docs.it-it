---
title: Configurare le chiavi gestite dal cliente per l'API di Azure per FHIR
description: La funzionalità Bring Your Own Key (BYOK) è supportata nell'API di Azure per FHIR tramite Cosmos DB
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 09/28/2020
ms.author: matjazl
ms.openlocfilehash: 05c208ba3c9005d38b8924037748764f8d112e3a
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398182"
---
# <a name="configure-customer-managed-keys"></a>Configurare le chiavi gestite dal cliente

Quando si crea un nuovo account dell'API di Azure per FHIR, per impostazione predefinita i dati vengono crittografati usando chiavi gestite da Microsoft. A questo punto, è possibile aggiungere un secondo livello di crittografia per i dati usando una chiave personalizzata che si sceglie e si gestisce in autonomia.

In Azure questa operazione viene in genere eseguita usando una chiave di crittografia nell'istanza di Azure Key Vault del cliente. SQL di Azure, Archiviazione di Azure e Cosmos DB sono alcuni esempi che attualmente forniscono questa funzionalità. L'API di Azure per FHIR sfrutta questo supporto da Cosmos DB. Quando si crea un account, sarà possibile specificare un URI della chiave di Azure Key Vault. Questa chiave verrà passata a Cosmos DB quando viene effettuato il provisioning dell'account del database. Quando viene inviata una richiesta a FHIR, Cosmos DB recupera la chiave e la usa per crittografare/decrittografare i dati. Per iniziare, vedere i collegamenti seguenti:

- [Registrare il provider di risorse Azure Cosmos DB per la sottoscrizione di Azure](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 
- [Configurare l'istanza di Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#configure-your-azure-key-vault-instance)
-  [Aggiungere un criterio di accesso all'istanza di Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)
- [Generare una chiave in Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#generate-a-key-in-azure-key-vault)

Dopo aver creato l'accouunt dell'API di Azure per FHIR in portale di Azure, verrà visualizzata un'opzione di configurazione "Crittografia dei dati" in "Impostazioni database" nella scheda "Impostazioni aggiuntive". Per impostazione predefinita, verrà scelta l'opzione Chiave gestita dal servizio. È possibile specificare la chiave di Azure Key Vault qui selezionando l'opzione "Chiave gestita dal cliente". Qui è possibile immettere l'URI della chiave copiato.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Creare l'API di Azure per FHIR":::

In alternativa, è possibile scegliere la chiave in Selezione chiavi:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="Selezione chiavi":::

Per gli account FHIR esistenti, è possibile visualizzare la scelta relativa alla chiave di crittografia (chiave gestita dal servizio o dal cliente) nel pannello "Database" come illustrato di seguito. Non è possibile modificare l'opzione di configurazione una volta scelta. Sarà tuttavia possibile modificare e aggiornare la chiave.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Database":::

È inoltre possibile creare una nuova versione della chiave specificata. Successivamente i dati verranno crittografati con la nuova versione senza interruzioni del servizio. Si può anche rimuovere l'accesso alla chiave per rimuovere l'accesso ai dati.
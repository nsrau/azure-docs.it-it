---
title: Crittografia di Azure sinapsi Analytics
description: Articolo che illustra la crittografia in Azure sinapsi Analytics
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 11/19/2020
ms.author: nanditav
ms.reviewer: jrasnick
ms.openlocfilehash: a6ea3925f3b6bc786be6a4855b2f3bfb6b402d70
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96455186"
---
# <a name="encryption-for-azure-synapse-analytics-workspaces"></a>Crittografia per le aree di lavoro di Azure sinapsi Analytics

In questo articolo verrà illustrato quanto segue:
* Crittografia dei dati inattivi nelle aree di lavoro di sinapsi Analytics.
* Configurazione delle aree di lavoro sinapsi per abilitare la crittografia con una chiave gestita dal cliente.
* Gestione delle chiavi usate per crittografare i dati nelle aree di lavoro.

## <a name="encryption-of-data-at-rest"></a>Crittografia dei dati inattivi

Una soluzione completa di crittografia dei dati inattivi garantisce che i dati non vengano mai mantenuti in un formato non crittografato. La doppia crittografia dei dati inattivi attenua le minacce con due livelli di crittografia distinti per proteggersi da compromessi di un singolo livello. Azure sinapsi Analytics offre un secondo livello di crittografia per i dati nell'area di lavoro con una chiave gestita dal cliente. Questa chiave è salvaguardata nel [Azure Key Vault](../../key-vault/general/overview.md), che consente di assumere la proprietà della gestione e della rotazione delle chiavi.

Il primo livello di crittografia per i servizi di Azure è abilitato con chiavi gestite dalla piattaforma. Per impostazione predefinita, i dischi di Azure e i dati negli account di archiviazione di Azure vengono crittografati automaticamente. Altre informazioni sul modo in cui viene usata la crittografia in Microsoft Azure nella [Panoramica di crittografia di Azure](../../security/fundamentals/encryption-overview.md).

## <a name="azure-synapse-encryption"></a>Crittografia delle sinapsi di Azure

Questa sezione consente di comprendere meglio come abilitare e applicare la crittografia delle chiavi gestite dal cliente nelle aree di lavoro sinapsi. Questa crittografia utilizza chiavi esistenti o nuove chiavi generate in Azure Key Vault. Una singola chiave viene usata per crittografare tutti i dati in un'area di lavoro. Le aree di lavoro sinapsi supportano chiavi RSA con chiavi di dimensioni di byte 2048 e 3072.

> [!NOTE]
> Le aree di lavoro sinapsi non supportano l'utilizzo di chiavi di crittografia Elliptic-Curve (ECC) per la crittografia.

I dati nei componenti sinapsi seguenti vengono crittografati con la chiave gestita dal cliente configurata a livello di area di lavoro:
* Pool SQL
 * Pool SQL dedicati
 * Pool SQL senza server
* Pool di Apache Spark
* Azure Data Factory runtime di integrazione, pipeline e set di impostazioni.

## <a name="workspace-encryption-configuration"></a>Configurazione della crittografia dell'area di lavoro

Le aree di lavoro possono essere configurate per abilitare la crittografia doppia con una chiave gestita dal cliente al momento della creazione dell'area di lavoro. Selezionare l'opzione "Abilita crittografia doppia usando una chiave gestita dal cliente" nella scheda "sicurezza" durante la creazione della nuova area di lavoro. È possibile scegliere di immettere un URI dell'identificatore di chiave oppure selezionare da un elenco di insiemi di credenziali delle chiavi nella **stessa area** dell'area di lavoro. Per il Key Vault è necessario che sia **abilitata la protezione ripulitura**.

> [!IMPORTANT]
> Al momento, l'impostazione di configurazione per la crittografia doppia non può essere modificata dopo la creazione dell'area di lavoro.

:::image type="content" source="./media/workspaces-encryption/workspaces-encryption.png" alt-text="Questo diagramma mostra l'opzione che è necessario selezionare per abilitare un'area di lavoro per la crittografia doppia con una chiave gestita dal cliente.":::

### <a name="key-access-and-workspace-activation"></a>Accesso alle chiavi e attivazione dell'area di lavoro

Il modello di crittografia di Azure sinapsi con chiavi gestite dal cliente prevede che l'area di lavoro acceda alle chiavi in Azure Key Vault per la crittografia e la decrittografia in base alle esigenze. Le chiavi vengono rese accessibili all'area di lavoro tramite criteri di accesso o Azure Key Vault accesso RBAC ([Anteprima](../../key-vault/general/rbac-guide.md)). Quando si concedono le autorizzazioni tramite un criterio di accesso Azure Key Vault, scegliere l'opzione "solo applicazione" durante la creazione dei criteri.

 All'identità gestita dell'area di lavoro devono essere concesse le autorizzazioni necessarie nell'insieme di credenziali delle chiavi prima che l'area di lavoro possa essere attivata. Questo approccio graduale all'attivazione dell'area di lavoro garantisce che i dati nell'area di lavoro vengano crittografati con la chiave gestita dal cliente. Si noti che la crittografia può essere abilitata o disabilitata per i pool SQL dedicati. ogni pool non è abilitato per la crittografia per impostazione predefinita.

#### <a name="permissions"></a>Autorizzazioni

Per crittografare o decrittografare i dati inattivi, l'identità gestita dell'area di lavoro deve avere le autorizzazioni seguenti:
* WrapKey (per inserire una chiave in Key Vault durante la creazione di una nuova chiave).
* UnwrapKey (per ottenere la chiave per la decrittografia).
* Get (per leggere la parte pubblica di una chiave)

#### <a name="workspace-activation"></a>Attivazione dell'area di lavoro

Dopo la creazione dell'area di lavoro (con crittografia doppia abilitata), rimarrà in stato "in sospeso" fino a quando l'attivazione avrà esito positivo. Per poter utilizzare completamente tutte le funzionalità, è necessario attivare l'area di lavoro. Ad esempio, è possibile creare un nuovo pool SQL dedicato solo dopo che l'attivazione ha avuto esito positivo. Concedere all'area di lavoro identità gestita l'accesso all'insieme di credenziali delle chiavi e fare clic sul collegamento di attivazione nell'area di lavoro portale di Azure banner. Una volta completata l'attivazione, l'area di lavoro è pronta per essere usata con la garanzia che tutti i dati sono protetti con la chiave gestita dal cliente. Come indicato in precedenza, l'insieme di credenziali delle chiavi deve avere la protezione ripulitura abilitata per la riuscita dell'attivazione.

:::image type="content" source="./media/workspaces-encryption/workspace-activation.png" alt-text="Questo diagramma mostra il banner con il collegamento di attivazione per l'area di lavoro.":::


### <a name="manage-the-workspace-customer-managed-key"></a>Gestire la chiave gestita dal cliente dell'area di lavoro 

È possibile modificare la chiave gestita dal cliente utilizzata per crittografare i dati dalla pagina di **crittografia** nel portale di Azure. Qui è possibile scegliere una nuova chiave usando un identificatore di chiave oppure selezionare da insiemi di credenziali delle chiavi a cui si ha accesso nella stessa area dell'area di lavoro. Se si sceglie una chiave in un insieme di credenziali delle chiavi diverso da quelli usati in precedenza, concedere le autorizzazioni di identità gestite "Get", "Wrap" e "Unwrap" del nuovo insieme di credenziali delle chiavi per l'area di lavoro. L'area di lavoro convaliderà l'accesso al nuovo insieme di credenziali delle chiavi e tutti i dati nell'area di lavoro verranno crittografati nuovamente con la nuova chiave.

:::image type="content" source="./media/workspaces-encryption/workspace-encryption-management.png" alt-text="Questo diagramma mostra la sezione relativa alla crittografia dell'area di lavoro nella portale di Azure.":::

I criteri di insieme di credenziali delle chiavi di Azure per la rotazione periodica automatica delle chiavi o le azioni sulle chiavi possono comportare la creazione di nuove versioni chiave. È possibile scegliere di crittografare di nuovo tutti i dati nell'area di lavoro con la versione più recente della chiave attiva. Per eseguire di nuovo la crittografia, modificare la chiave nel portale di Azure in una chiave temporanea e quindi tornare alla chiave che si vuole usare per la crittografia. Ad esempio, per aggiornare la crittografia dei dati usando la versione più recente di key1 chiave attiva, modificare la chiave gestita dal cliente dell'area di lavoro in chiave temporanea, Key2. Attendere il completamento della crittografia con Key2. Passare quindi la chiave gestita dal cliente dell'area di lavoro a Key1. i dati nell'area di lavoro verranno crittografati di nuovo con la versione più recente di key1.

> [!NOTE]
> Azure sinapsi Analytics sta lavorando attivamente per aggiungere funzionalità per crittografare di nuovo automaticamente i dati quando vengono create nuove versioni chiave. Fino a quando questa funzionalità non sarà disponibile, per garantire la coerenza nell'area di lavoro, forzare la nuova crittografia dei dati usando il processo descritto in precedenza.

#### <a name="sql-transparent-data-encryption-with-service-managed-keys"></a>Transparent Data Encryption SQL con chiavi gestite dal servizio

SQL Transparent Data Encryption (Transparent Data Encryption) è disponibile per i pool SQL dedicati nelle aree di lavoro *non* abilitate per la crittografia doppia. In questo tipo di area di lavoro viene usata una chiave gestita dal servizio per fornire la crittografia doppia per i dati nei pool SQL dedicati. Transparent Data Encryption con la chiave gestita dal servizio può essere abilitata o disabilitata per i singoli pool SQL dedicati.

## <a name="next-steps"></a>Passaggi successivi

[Usare i criteri predefiniti di Azure per implementare la protezione della crittografia per le aree di lavoro sinapsi](../policy-reference.md)


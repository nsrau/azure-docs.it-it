---
title: Come convertire i formati di token di sessione in .NET SDK-Azure Cosmos DB
description: Informazioni su come convertire i formati di token di sessione per garantire la compatibilità tra diverse versioni di .NET SDK
author: vinhms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/30/2020
ms.author: vitrinh
ms.custom: devx-track-csharp
ms.openlocfilehash: 7ed06eeb7330989b4a251dc77e516eb8ac578bff
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93342046"
---
# <a name="convert-session-token-formats-in-net-sdk"></a>Convertire i formati di token di sessione in .NET SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Questo articolo illustra come eseguire la conversione tra formati di token di sessione diversi per garantire la compatibilità tra le versioni dell'SDK.

> [!NOTE]
> Per impostazione predefinita, l'SDK tiene traccia automaticamente del token di sessione e utilizzerà il token di sessione più recente.  Per altre informazioni, vedere [usare i token di sessione](how-to-manage-consistency.md#utilize-session-tokens). Le istruzioni riportate in questo articolo si applicano solo con le condizioni seguenti:
> * L'account Azure Cosmos DB usa la coerenza di sessione.
> * La gestione dei token di sessione è manuale.
> * Si utilizzano contemporaneamente più versioni dell'SDK.

## <a name="session-token-formats"></a>Formati di token di sessione

Sono disponibili due formati di token di sessione: **semplice** e **vettore**.  Questi due formati non sono interscambiabili, pertanto è necessario convertire il formato quando si passa all'applicazione client con versioni diverse.
- Il formato del token di sessione **semplice** viene usato da .NET SDK V1 (Microsoft.Azure.DocumentDB-Version 1. x)
- Il formato del token di sessione **vector** viene usato da .NET SDK V2 (Microsoft.Azure.DocumentDB-Version 2. x)

### <a name="simple-session-token"></a>Token di sessione semplice

Un token di sessione semplice presenta questo formato: `{pkrangeid}:{globalLSN}`

### <a name="vector-session-token"></a>Token di sessione Vector

Un token di sessione Vector ha il formato seguente: `{pkrangeid}:{Version}#{GlobalLSN}#{RegionId1}={LocalLsn1}#{RegionId2}={LocalLsn2}....#{RegionIdN}={LocalLsnN}`

## <a name="convert-to-simple-session-token"></a>Converti in token di sessione semplice

Per passare un token di sessione al client usando .NET SDK V1, usare un formato di token di sessione **semplice** .  Usare, ad esempio, il codice di esempio seguente per convertirlo.

```csharp
private static readonly char[] SegmentSeparator = (new[] { '#' });
private static readonly char[] PkRangeSeparator = (new[] { ':' });

// sessionTokenToConvert = session token from previous response
string[] items = sessionTokenToConvert.Split(PkRangeSeparator, StringSplitOptions.RemoveEmptyEntries);
string[] sessionTokenSegments = items[1].Split(SessionTokenHelpers.SegmentSeparator, StringSplitOptions.RemoveEmptyEntries);

string sessionTokenInSimpleFormat;

if (sessionTokenSegments.Length == 1)
{
    // returning the same token since it already has the correct format
    sessionTokenInSimpleFormat = sessionTokenToConvert;
}
else
{
    long version = 0;
    long globalLSN = 0;

    if (!long.TryParse(sessionTokenSegments[0], out version)
        || !long.TryParse(sessionTokenSegments[1], out globalLSN))
    {
        throw new ArgumentException("Invalid session token format", sessionTokenToConvert);
    }

    sessionTokenInSimpleFormat = string.Format("{0}:{1}", items[0], globalLSN);
}
```

## <a name="convert-to-vector-session-token"></a>Converti in token di sessione vettoriale

Per passare un token di sessione al client usando .NET SDK v2, usare il formato di token della sessione **vettoriale** .  Usare, ad esempio, il codice di esempio seguente per convertirlo.

```csharp

private static readonly char[] SegmentSeparator = (new[] { '#' });
private static readonly char[] PkRangeSeparator = (new[] { ':' });

// sessionTokenToConvert = session token from previous response
string[] items = sessionTokenToConvert.Split(PkRangeSeparator, StringSplitOptions.RemoveEmptyEntries);
string[] sessionTokenSegments = items[1].Split(SegmentSeparator, StringSplitOptions.RemoveEmptyEntries);

string sessionTokenInVectorFormat;

if (sessionTokenSegments.Length == 1)
{
    long globalLSN = 0;
    if (long.TryParse(sessionTokenSegments[0], out globalLSN))
    {
        sessionTokenInVectorFormat = string.Format("{0}:-2#{1}", items[0], globalLSN);
    }
    else
    {
        throw new ArgumentException("Invalid session token format", sessionTokenToConvert);
    }
}
else
{
    // returning the same token since it already has the correct format
    sessionTokenInVectorFormat = sessionTokenToConvert;
}
```

## <a name="next-steps"></a>Passaggi successivi

Leggere gli articoli seguenti:

* [Usare token di sessione per gestire la coerenza in Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Scegliere il livello di coerenza ottimale in Azure Cosmos DB](./consistency-levels.md)
* [Coerenza, disponibilità e compromessi delle prestazioni in Azure Cosmos DB](./consistency-levels.md)
* [Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza](./consistency-levels.md)
---
title: "Creare entità ContentKey mediante .NET"
description: Informazioni su come creare chiavi simmetriche che forniscono l&quot;accesso sicuro agli asset.
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 225b05e5-7d30-409c-b5b7-3ef0634310c7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c547afd9535eb4764caf6b37d4e38a22f6e88186


---
# <a name="create-contentkeys-with-net"></a>Creare entità ContentKey mediante .NET
> [!div class="op_single_selector"]
> * [REST](media-services-rest-create-contentkey.md)
> * [.NET](media-services-dotnet-create-contentkey.md)
> 
> 

Servizi multimediali consente di creare asset crittografati e distribuirli. Un'entità **ContentKey** consente l'accesso sicuro alle entità **Asset**. 

Quando si crea un nuovo asset, ad esempio prima di [caricare file](media-services-dotnet-upload-files.md), è possibile specificare le seguenti opzioni di crittografia: **StorageEncrypted**, **CommonEncryptionProtected** o **EnvelopeEncryptionProtected**. 

Quando si distribuiscono asset ai client, è possibile [configurarli per la crittografia dinamica](media-services-dotnet-configure-asset-delivery-policy.md) con una delle due seguenti opzioni: **DynamicEnvelopeEncryption** o **DynamicCommonEncryption**.

Gli asset crittografati devono essere associati alle entità **ContentKey**. Questo articolo descrive come creare una chiave simmetrica.

> [!NOTE]
> Quando si crea un nuovo asset **StorageEncrypted** mediante Media Services .NET SDK, l'entità **ContentKey** viene creata automaticamente e collegata all'asset.
> 
> 

## <a name="contentkeytype"></a>ContentKeyType
Uno dei valori che è necessario impostare quando si crea una chiave simmetrica è quello relativo al tipo. È possibile scegliere uno dei seguenti valori. 

    public enum ContentKeyType
    {
        /// <summary>
        /// Specifies a content key for common encryption.
        /// </summary>
        /// <remarks>This is the default value.</remarks>
        CommonEncryption = 0,

        /// <summary>
        /// Specifies a content key for storage encryption.
        /// </summary>
        StorageEncryption = 1,

        /// <summary>
        /// Specifies a content key for configuration encryption.
        /// </summary>
        ConfigurationEncryption = 2,

        /// <summary>
        /// Specifies a content key for Envelope encryption.  Only used internally.
        /// </summary>
        EnvelopeEncryption = 4
    }

## <a name="a-idenvelopecontentkeyacreate-envelope-type-contentkey"></a><a id="envelope_contentkey"></a>Creare un'entità ContentKey di tipo envelope
Il seguente frammento di codice crea una chiave simmetrica con tipo di crittografia envelope. Associa quindi la chiave all'asset specificato.

    static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
    {
        // Create envelope encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.EnvelopeEncryption);

        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int size)
    {
        byte[] randomBytes = new byte[size];
        using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
        {
            rng.GetBytes(randomBytes);
        }

        return randomBytes;
    }

chiamare

    IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);



## <a name="a-idcommoncontentkeyacreate-common-type-contentkey"></a><a id="common_contentkey"></a>Creare un'entità ContentKey di tipo common
Il seguente frammento di codice crea una chiave simmetrica con tipo di crittografia common. Associa quindi la chiave all'asset specificato.

    static public IContentKey CreateCommonTypeContentKey(IAsset asset)
    {
        // Create common encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.CommonEncryption);

        // Associate the key with the asset.
        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int length)
    {
        var returnValue = new byte[length];

        using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
        {
            rng.GetBytes(returnValue);
        }

        return returnValue;
    }
chiamare

    IContentKey key = CreateCommonTypeContentKey(encryptedsset); 


## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Nov16_HO3-->



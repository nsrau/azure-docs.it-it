---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/31/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fc2393cfe87e2639ce40e66e6053d4d430518719
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2020
ms.locfileid: "87515341"
---
## <a name="1-download-the-file"></a>1. Scaricare il file

Eseguire i comandi seguenti. Copiare l'URL dei risultati nel browser per scaricare il file con estensione zip del profilo.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. Estrarre il file con estensione zip

Estrarre il file con estensione zip. Il file contiene le cartelle seguenti:

* AzureVPN
* Generico
* OpenVPN (se è stata abilitata la funzionalità OpenVPN con il **certificato di Azure** o le impostazioni di **autenticazione RADIUS** sul gateway). Per informazioni sul gateway VPN, vedere [Creare un tenant](../articles/vpn-gateway/openvpn-azure-ad-tenant.md). Per informazioni sulla rete WAN virtuale, vedere [Creare un tenant - VWAN](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

## <a name="3-retrieve-information"></a>3. Recuperare le informazioni

Nella cartella **AzureVPN** selezionare il file ***azurevpnconfig.xml*** e aprirlo con Blocco note. Prendere nota del testo tra i tag seguenti.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Dettagli del profilo

Quando si aggiunge una connessione, è necessario specificare le informazioni raccolte nel passaggio precedente nella pagina dei dettagli del profilo. I campi corrispondono alle informazioni seguenti:

   * **Destinatari:** Identifica la risorsa di destinazione del token
   * **Autorità di certificazione:** Identifica il servizio token di sicurezza che ha rilasciato il token e il tenant di Azure AD
   * **Tenant:** Contiene un identificatore univoco e non modificabile del tenant di directory che ha emesso il token
   * **FQDN:** Nome di dominio completo del gateway VPN di Azure
   * **ServerSecret:** Chiave precondivisa del gateway VPN

## <a name="folder-contents"></a>Contenuto cartelle

* La **cartella generica** contiene il certificato del server pubblico e il file VpnSettings.xml. Il file VpnSettings.xml contiene le informazioni necessarie per configurare un client generico.

* Il file con estensione zip scaricato può contenere anche le cartelle **WindowsAmd64** e **WindowsX86**. Queste cartelle includono il programma di installazione per SSTP e IKEv2 per i client Windows. Per eseguire l'installazione, è necessario disporre dei diritti di amministratore per il client.

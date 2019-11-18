---
title: 'Gateway VPN di Azure: informazioni sui profili client VPN P2S'
description: Questo consente di usare il file del profilo client
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 5386cace7191be60534f0d2fbf4a85b592d1ecdd
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151633"
---
# <a name="about-p2s-vpn-client-profiles"></a>Informazioni sui profili client VPN P2S

Il file del profilo scaricato contiene le informazioni necessarie per configurare una connessione VPN. Questo articolo consente di ottenere e comprendere le informazioni necessarie per un profilo client VPN.

## <a name="1-download-the-file"></a>1. scaricare il file

Eseguire i comandi seguenti. Copiare l'URL dei risultati nel browser per scaricare il file zip del profilo.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. Estrarre il file zip

Estrarre il file con estensione zip. Il file contiene le cartelle seguenti:

* AzureVPN
* Generico
* OpenVPN (se sono state abilitate le impostazioni di autenticazione OpenVPN e Azure AD sul gateway. Vedere [creare un tenant](openvpn-azure-ad-tenant.md).

## <a name="3-retrieve-information"></a>3. recuperare informazioni

Nella cartella **AzureVPN** passare al file ***azurevpnconfig. XML*** e aprirlo con il blocco note. Prendere nota del testo tra i seguenti tag.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Dettagli profilo

Quando si aggiunge una connessione, usare le informazioni raccolte nel passaggio precedente per la pagina dei dettagli del profilo. I campi corrispondono alle informazioni seguenti:

   * **Destinatari:** Identifica la risorsa destinatario a cui è destinato il token
   * **Autorità di certificazione:** Identifica il servizio token di sicurezza (STS) che ha emesso il token e il tenant di Azure AD
   * **Tenant:** Contiene un identificatore univoco e non modificabile del tenant di directory che ha emesso il token.
   * **FQDN:** Nome di dominio completo (FQDN) nel gateway VPN di Azure
   * **ServerSecret:** Chiave precondivisa del gateway VPN

## <a name="folder-contents"></a>Contenuto della cartella

* La **cartella OpenVPN** contiene il profilo *ovpn* che deve essere modificato per includere la chiave e il certificato. Per altre informazioni, vedere [configurare i client OpenVPN per il gateway VPN di Azure](vpn-gateway-howto-openvpn-clients.md#windows).

* La **cartella Generic** contiene il certificato del server pubblico e il file file vpnsettings. XML. Il file file vpnsettings. XML contiene le informazioni necessarie per configurare un client generico.

* Il file zip scaricato può contenere anche cartelle **WindowsAmd64** e **WindowsX86** . Queste cartelle contengono il programma di installazione per SSTP e IKEv2 per i client Windows. Per installarli, è necessario disporre dei diritti di amministratore per il client.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su da punto a sito, vedere [informazioni su da punto a sito](point-to-site-about.md).

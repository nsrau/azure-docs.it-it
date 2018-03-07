Quando viene generato un certificato client, viene automaticamente installato nel computer che è stato usato per generarlo. Se si vuole installare un certificato client in un altro computer client, è necessario esportare il certificato client generato.

1. Per esportare un certificato client, aprire **Gestire i certificati utente**. Per impostazione predefinita, i certificati client generati si trovano in "Certificati-Utente corrente\Personale\Certificati". Fare clic con il pulsante destro del mouse sul certificato client da esportare, scegliere **Tutte le attività**, quindi fare clic su **Esporta** per aprire **Esportazione guidata certificati**.

  ![Esportazione](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. In Esportazione guidata certificati fare clic su **Avanti** per continuare.

  ![Avanti](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. Selezionare **Sì, esporta la chiave privata** e quindi fare clic su **Avanti**.

  ![esporta la chiave privata](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. Nella pagina **Formato file di esportazione** lasciare selezionate le impostazioni predefinite. Verificare che l'opzione **Se possibile, includi tutti i certificati nel percorso certificazione** sia selezionata. Questa opzione consente anche l'esportazione delle informazioni del certificato radice necessarie per la corretta autenticazione del client. Senza tali informazioni, l'autenticazione del client ha esito negativo perché il client non ha il certificato radice attendibile. Quindi fare clic su **Next**.

  ![formato del file di esportazione](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. Nella pagina **Sicurezza** è necessario proteggere la chiave privata. Se si sceglie di usare una password, assicurarsi di registrare o ricordare quella impostata per questo certificato. Quindi fare clic su **Next**.

  ![security](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. In **File da esportare** fare clic su **Sfoglia** e passare alla posizione in cui si vuole esportare il certificato. Per **Nome file**, assegnare un nome al file del certificato. Quindi fare clic su **Next**.

  ![file da esportare](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. Fare clic su **Fine** per esportare il certificato.

  ![fine](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)
## Scenario

In questo documento verrà illustrata una distribuzione che usa un indirizzo IP pubblico statico allocato a una macchina virtuale (VM). In questo scenario, si dispone di una singola VM con il proprio indirizzo IP pubblico statico. La VM fa parte di una subnet denominata **FrontEnd** e dispone anche di un indirizzo IP privato statico (**192.168.1.101**) nella subnet.

Potrebbe essere necessario un indirizzo IP statico per i server Web che richiedono connessioni SSL in cui il certificato SSL è collegato a un indirizzo IP.

![DESCRIZIONE DELL’IMMAGINE](./media/virtual-network-deploy-static-pip-scenario-include/figure1.png)

È possibile seguire i passaggi riportati sotto per distribuire l'ambiente illustrato nella figura precedente.

<!---HONumber=AcomDC_0114_2016-->
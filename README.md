# treinamento 

// transformação da venda (cabeça + item)
[NF Saida]:
LOAD
	F2_FILIAL & '|' & F2_DOC & '|' & F2_SERIE 		as %venda,
    F2_FILIAL										as %filial,
    F2_DOC											as [Numero da NF de saida],
    F2_SERIE										as [Serie da NF de Saida],
    F2_CLIENTE & '|' & F2_LOJA						as %cliente,
    //F2_LOJA,
    F2_COND											as %condicaoPagamento,
    F2_EMISSAO2										as [Data Emissão NF de saida]
FROM [lib://Storage/1.Extrator\QVD\SF2010.qvd](qvd)
where D_E_L_E_T_ <> '*';

Left Join

LOAD
	D2_FILIAL & '|' & D2_DOC & '|' & D2_SERIE		as %venda,
    //D2_FILIAL,
    //D2_ITEM,
    D2_COD											as %produto,
    D2_TES											as %tes,
    //D2_SEGUM,
    D2_UM											as [unidade de medida],
    D2_QUANT										as [quantidade de venda],
    D2_PRCVEN										as [preço de unitario de venda],
    D2_TOTAL										as [valor da venda],
    (D2_TOTAL/D2_QUANT)								as [preco medio]
    
FROM [lib://Storage/1.Extrator\QVD\SD2010.qvd](qvd)
where D_E_L_E_T_ <> '*';

store [NF Saida] into [lib://Storage/2.Transformador/QVD/notafiscal_saida.qvd](qvd);
drop Table [NF Saida];

// transformação da compra (cabeça + item)
[NF Compra]:
LOAD
	F1_FILIAL & '|' & F1_DOC & '|' & F1_SERIE & '|' & F1_FORNECE & '|' & F1_LOJA	as %compra, 
    F1_FILIAL										as %filial,
    F1_DOC											as [Numero NF de Entrada],
    F1_SERIE										as [Serie NF de Entrada],
    F1_FORNECE & '|' & F1_LOJA						as %fornecedor,
    F1_COND											as %condicaoPagamento,
    F1_EMISSAO2										as [Data Emissao NF Entrada]
FROM [lib://Storage/1.Extrator\QVD\SF1010.qvd](qvd)
where D_E_L_E_T_ <> '*';

Left Join

LOAD
	D1_FILIAL & '|' & D1_DOC & '|' & D1_SERIE & '|' & D1_FORNECE & '|' & D1_LOJA as %compra,    
    //D1_ITEM,
    D1_COD											as %produto,
    D1_UM											as [unidade de medida],
    //D1_SEGUM,
    D1_QUANT										as [quantidade compra],
    D1_VUNIT										as [preço unitario compra],
    D1_TOTAL										as [valor da compra],
    D1_VALIPI,
    D1_VALICM,
    D1_TES											as %tes
FROM [lib://Storage/1.Extrator\QVD\SD1010.qvd](qvd)
where D_E_L_E_T_ <> '*';

    
store [NF Compra] into [lib://Storage/2.Transformador/QVD/notafiscal_compra.qvd](qvd);
drop Table [NF Compra];

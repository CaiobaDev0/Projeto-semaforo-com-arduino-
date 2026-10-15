// ===== DECLARAÇÃO DE VARIÁVEIS =====
INTEIRO vermelhoC recebe PORTA 13
INTEIRO amarelo   recebe PORTA 12
INTEIRO verdeC    recebe PORTA 11
INTEIRO botao     recebe PORTA 2
INTEIRO vermelhoP recebe PORTA 10
INTEIRO verdeP    recebe PORTA 9
INTEIRO buzzer    recebe PORTA 7
INTEIRO cancela   recebe PORTA 4
INTEIRO fotossensor recebe A0
INTEIRO trigDist  recebe PORTA 5
INTEIRO echoDist  recebe PORTA 6

INTEIRO tempo recebe 0
BOOLEANO dia recebe VERDADEIRO
BOOLEANO pedidoPedestre recebe FALSO

// ===== SETUP =====
INICIO SETUP
    CONFIGURAR vermelhoC COMO SAIDA
    CONFIGURAR amarelo   COMO SAIDA
    CONFIGURAR verdeC    COMO SAIDA
    CONFIGURAR vermelhoP COMO SAIDA
    CONFIGURAR verdeP    COMO SAIDA
    CONFIGURAR buzzer    COMO SAIDA
    CONFIGURAR cancela   COMO SAIDA (servo)

    CONFIGURAR botao       COMO ENTRADA
    CONFIGURAR fotossensor COMO ENTRADA
    CONFIGURAR trigDist    COMO SAIDA
    CONFIGURAR echoDist    COMO ENTRADA

    tempo recebe 0
    ATIVAR INTERRUPCAO(botao, chamarFuncao: BotaoPedestre)
FIM SETUP

// ===== LOOP PRINCIPAL =====
INICIO LOOP
    dia recebe LER(fotossensor) > LIMIAR_LUZ
    tempo recebe tempo + 1

    SE (dia) ENTAO
        SE (tempo >= 0 E tempo <= 25) ENTAO
            verdeC ON
            vermelhoP ON

        SENAO SE (tempo > 25 E tempo <= 30) ENTAO
            amarelo ON

        SENAO SE (tempo > 30 E tempo <= 45) ENTAO
            vermelhoC ON
            verdeP ON
            BUZZER ON (toques de 0,5 segundo)

        SENAO
            tempo recebe 0   // reinicia o ciclo
        FIM SE

    SENAO  // noite
        PISCAR(amarelo, intervalo: 1 segundo)
    FIM SE

    VerificarCancela()
FIM LOOP

// ===== FUNÇÃO: BOTÃO DO PEDESTRE (interrupção) =====
INICIO FUNCAO BotaoPedestre()
    SE (verdeC ESTA ON) ENTAO
        amarelo ON
        ESPERAR(3 segundos)
        verdeC OFF
        vermelhoC ON
        verdeP OFF
        vermelhoP ON
    FIM SE
FIM FUNCAO

// ===== FUNÇÃO: CANCELA / SENSOR DE DISTÂNCIA =====
INICIO FUNCAO VerificarCancela()
    distancia recebe MEDIR_DISTANCIA(trigDist, echoDist)

    SE (distancia <= 30 cm) ENTAO
        cancela recebe ABRIR(90 graus)
        ESPERAR(3 segundos)
    SENAO
        cancela recebe FECHAR
    FIM SE
FIM FUNCAO

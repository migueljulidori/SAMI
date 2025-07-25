# SAMI
SAMI (sistema atomatizado de medicação inteligente) é um projeto feito para o biochalenge inatel 2025.

História para contextualização:
Um casal de idosos (Sr. Antônio e Sra. Dolores) tomam alguns remédios mas vivem confundindo os horários e os medicamentos, que possuem embalagens semelhantes.

Pensando nessa história, definimos um projeto (SAMI) para ajudar esses idosos a controlar melhor seus remédios. 

Explicação técnica e detalhada:
O sistema funciona da seguinte maneira, o idoso precisa se posicionar em frente a uma camera que tem um sistema de reconhecimento facial e inteligência artificial em python para reconhecer qual idoso deve tomar o remédio (Sr. Antônio ou Sra. Dolores), para o codigo em python reconhecer os rostos foi necessário importar as seguintes bibliotecas:

CV2: A biblioteca OpenCV, usada para capturar vídeo da webcam e desenhar retângulos/textos sobre os rostos detectados.

OS: Permite acessar pastas e arquivos, importante para abrir as imagens dos rostos cadastrados.

NUMPY: Utilizada para cálculos matemáticos, especialmente a comparação entre rostos.

INSIGHTFACE: Biblioteca de reconhecimento facial open-source baseada em redes neurais e FACEANALYSIS.

SERIAL: Permite comunicação com o Arduino via porta USB.

TIME: Usada para pausar a execução quando necessário (ex: aguardar o Arduino reiniciar).

No inicio do código em python é necessário abrir a comunicação serial com o microcontrolador na porta COM5 com uma taxa de 9600 bits por segundo, no código também é implementado um "time.sleep()" que garante que o microcontrolador tenha tempo para reiniciar após ser conectado, o sistema acessa uma pasta "imagens/", onde ficam armazenadas as fotos dos rostos das pessoas cadastradas, se o rosto for detectado, a assinatura facial (embedding) é salva no dicionário "rostos_conhecidos" com o nome do arquivo como chave, rosto identificado ou se não detectar nenhum rosto é avisado no terminal do VScode, o código contém um loop que captura continuamente imagens da câmera(frame), ao identificar os rostos o código compara o embedding com as assinaturas armazenadas (imagens/), a comparação é feita com distância Euclidiana: quanto menor a distância, mais parecido é o rosto, se o rosto não for suficientemente parecido (distância >= 19) ele é tratado como desconhecido, após essa etapa o código em python do reconhecimento manda a informação se o rosto visto é conhecido ou não, se for conhecido o código manda o nome em forma de texto (uma string) para um microcontrolador via comunicação serial, com essa informação dentro do microcontrolador o código no Arduino IDE compara se está na hora do idoso reconhecido tomar seu devido remédio, para fazer essa camparação o arduino precisa de um cronômetro e uma definição de quando é a hora do idoso tomar o seu remédio, portanto, para o cronômetro foi usado o comando "millis()" que define um timer de 24 horas dentro do arduino, para a outra parte da comparação foi utilizado o comando "struct * {}" que serve para estruturar o idoso definindo seu nome, hora e minuto para tomar o remédio e qual remédio deve ser liberado para esse idoso, após essas informações é criado um "vetor de estruturas" idoso[], onde cada vetor representa um idoso, por exemplo "{"Sr.Antonio", 10, 1, SERVO_ANTONIO, false}", depois disso é criado uma constante para a quantidade de idosos, o comando calcula automaticamente quantos idosos há no vetor, essa constante é igualada ao sizeof(idosos)/sizeof(idoso), onde o "sizeof(idosos)" retorna o tamanho total em bytes de todos os elementos do vetor e o "sizeof(Idoso)" retorna o tamanho de apenas um Idoso, essa divisão é útil para fazer loops for sem precisar alterar manualmente se você adicionar mais idosos depois, depois de todos esses passos o microcontrolador compara e libera o remédio correto. O sitema tem 3 compartimentos para guardar os remédios, para identificar se o compartimento está cheio ou vazio foi colocado um sensor ultrassônico HC-SR04 que lê se há ou não remédios no compartimento através da leitura de seus pinos TRIG e ECHO, o microcontrolador manda um pulso pelo pino TRIG que sai como um pulso ultrassônico (inaudível), esse som viaja no ar, bate em um obstáculo (remédio ou parede) e volta, o pino ECHO fica em nível alto (HIGH) durante o tempo que o som demorou para ir e voltar, o microcontrolador mede esse tempo e calcula a distância do sesor até o obstáculo, a partir desse funcionamento, o sistema pode identificar se o compartimento está cheio ou vazio, informando ao usuario através de LEDs e um Display LCD 16x2 I2C que mostra o horário, o rosto conhecido ou se é desconhecido, e se o compartimento necessita de reposição

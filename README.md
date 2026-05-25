# Projeto 4: Aprendizado por Reforço e Modelos Generativos Adversários (VAE-GAN)
---

## Visão Geral do Projeto & Contextualização RGB
Exploro implementação de um modelo híbrido de arquiteturas adversárias (VAE-GAN). O objetivo principal é comprimir a essência estrutural no espaço latente e gerar novas variantes sintéticas que testem o equilíbrio de um classificador(Discriminador).

 ---

## Curadoria do Dataset
Inicialmente, o projeto utilizaria um escopo amplo de imagens.Mas acabei decidindo por uma única imagem
**Foco no Abacate (*Avocado Green 1*):** A escolha do abacate foi estratégica por apresentar uma geometria ovalado e outos tipos semi-esférica, texturas rugosas e sombreamentos graduais. O modelo foi configurado em resolução de **64x64 pixels**, ideal para otimização de memória em GPU sem perda das nuances da casca.

---

## (Evolução Técnico-Científica)

O trabalho reside no processo de engenharia e refinamento para alcançar o **equilíbrio de forças**. Fases cruciais:

### Fase 1: O Modelo de Resolução Padrão
* **Abordagem:** O VAE tentava reconstruir a imagem adicionando um ruído gaussiano simples no vetor latente ($z$). 
    * **Resultado:** O modelo gerou abacates muito estáticos. Ele apenas suavizava as bordas e reduzia a resolução geral da imagem, sem gerar bizarrices ou deformações estruturais de impacto. **O DISCRIMINADOR VENCIA SEMPRE COM FACILIDADE**.

### Fase 2: Engenharia Latente (Super Gerador OP - Solução Final)
* **Abordagem:** Percebendo que a deformação precisava nascer *dentro* da estrutura da rede, mudei drasticamente a arquitetura:
  * **Classificador Simplificado:** Removi o `BatchNorm2d` do Discriminador e reduzi seus filtros de canais pela metade (16 e 32), tornando-o ligeiramente "míope" e tolerante.
  * **Superpoderes ao VAE (Gerador):** O peso da perda adversária do VAE foi multiplicado por 10 (subindo de `0.05` para `0.50`).
  * **Inversão de Código Latente:** Na fase de testes, usei a função `torch.flip` em metades específicas do vetor latente de 128 dimensões. Como o espaço latente mapeia características espaciais, inverter suas coordenadas forçou o decodificador a desenhar o abacate de forma nativamente assimétrica, derretida e bizarra.

---

## Análise Gráfica das Losses

O comportamento final das redes trouxe o cenário ideal para a defesa deste projeto:

### Interpretação do Gráfico de Treinamento
* **Curva Azul (Loss do VAE/Gerador):** Apresentou decaimento constante e suave, iniciando em `1.2` a  `1.4` e estabilizando próximo de  `0.6` a `0.4`. Isso valida que o Gerador aprendeu com precisão **a criar** texturas orgânicas e preenchimentos verdes realistas.
* **Curva Vermelha (Loss do Discriminador):** Após as rodadas de ajuste, estabilizou-se em uma **linha horizontal plana estável** ao redor de `1.4`. Em arquiteturas adversárias, essa estabilização no alto significa que o classificador foi forçado a um estado de **incerteza controlada e saudável, permitindo a flutuação de acertos e erros.**

### O Placar de Acertos (Veredicto das Variantes)
A eficácia da engenharia latente é comprovada na exibição das 10 variantes geradas:
* **(Fraqueza da IA):** O Gerador conseguiu enganar completamente o classificador, obtendo notas de realismo superiores a `0.50` (ex: `0.54`, a  `0.51`). O modelo gerou abacates com texturas tão convincentes que a IA os classificou erroneamente como reais.
* **(Sucesso da IA):** À medida que a inversão e a distorção dos blocos latentes tornaram-se extremas, a assimetria geométrica rompeu o limite "normal" da fruta. O Discriminador recuperou o controle e barrou as fraudes com sucesso, emitindo o veredicto correto de imagem FALSA.

---
 rodei tudo no kaggle o link do dataset é esse https://www.kaggle.com/datasets/moltean/fruits

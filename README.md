# t1_paralela_jogo_da_vida_openmp

Relatório de Entrega de Trabalho 1 da disciplina de Programação Paralela 

Prof. Marcelo Veiga Neves
Giovanni Caprio (pp12813) e Guilherme Prates


Implementação 

O presente trabalho consiste na implementação do jogo Conway's Game Of Life (Joga da Vida) em linguagem C de duas formas distintas: uma serial e outra paralela através das diretrizes OpenMP. O objetivo dessa atividade  é avaliar o ganho de desempenho em um nodo multiprocessado do cluster Grad de um programa paralelo. Primeiramente foi definido que o problema alvo será o especificado pelo enunciado do trabalho: o jogo Conway's Game Of Life. Para tanto, foi utilizado uma versão serial pronta do jogo que foi desenvolvida por Ali Ahmed. 
Apesar de pronto, o código base utilizado para a versão sequencial do trabalho foi alterado. O programa de Ahmed possui uma apresentação do tabuleiro pouco intuitiva, bem como possui em meio ao código algumas estruturas que o tornavam lento (printf, sleep e funções de desenho do tabuleiro) que foram removidas e/ou adaptadas para a versão final utilizada. O tabuleiro do código serial, que posteriormente será transformado em paralelo, possui 175 linhas com 40 colunas. Além disso, uma das características dessa implementação é de que o tabuleiro não fica "limpo", pois quando as células avançam para além dos limites da matriz elas automaticamente aparecem na posição oposta. Por exemplo, se uma célula sair pela parte de baixo do board a mesma será realocada na parte  superior do mesmo. Sendo assim o número de jogadas deve ser especificado e nesse caso será sempre submetido ao total de 10.000 jogadas (iterações).
Por fim, todos os códigos desenvolvidos e/ou adaptados foram avaliados na máquina Atlantica do Laboratório de Alto Desempenho (LAD) da PUCRS e estão disponíveis no github. Foram alocados de forma exclusiva 8 cores físicos, totalizando 16 com Hyper-Threading, do cluster Grad. Nessa máquina o desempenho programa serial foi de 5.756345 segundos, valor que servirá como parâmetro para os próximos testes. 

Estratégia de paralelismo

A abordagem utilizada na paralelização do código foi definida após a observação do comportamento do programa serial, cuja forma de atuação baseia-se na iteração por quadrantes de cada elemento do tabuleiro. Cada peça selecionada passa por inspeção de seus vizinhos para a aplicação da regra do jogo e a criação de um novo tabuleiro após o final da rodada. Dessa forma, a estratégia adotado foi paralelizar o código para abranger uma região maior sem que houvesse a necessidade de percorrer peça por peça de forma sequencial. Sendo assim, baseados na ideia de expandir a "visão" do algoritmo sobre o tabuleiro, foram paralelizadas as linhas e as colunas. Assim, cada thread estaria selecionando as colunas que desejasse sem estar presa a qual tupla linha x coluna está sendo processada no momento, criando uma espécie de spanning tree. 
	Essa estratégia se mostrou acertada, vide tabela 1 de tempo de execução e speedup abaixo, na qual é possível perceber que conforme o número de threads cresce, o tempo diminui. 
  			Tabela 1.
O desempenho do programa paralelo está exemplificado no gráfico abaixo, onde percebe-se que até 8 threads a eficiência é crescente, porém a partir desse número ela passa a ficar estagnada. Neste trabalho optou-se por não utilizar a estratégia de escalonamento dinâmica visto que, pelo mapa inicial, a maioria das atividades teriam o mesmo tempo. Sendo assim, o overhead para implementar o dinamismo poderia prejudicar o desempenho do algoritmo. 


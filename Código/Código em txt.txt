# Feito por: Guilherme Gibeli e Eduardo Dobbins

"""import log2 da biblioteca math"""
from math import log2

"""import randint da biblioteca random"""
from random import randint

def options():
    """Exibe as opções do menu."""

    print('\nColoque qual Mapeamento deseja')
    print('1 - Mapeamento associativo por Conjunto')
    print('Qualquer outro valor para encerrar')

def MAPR():
    """Executa o mapeamento associativo por conjunto."""

    def ler_arquivo(arquivo):
        """Lê o arquivo e retorna uma lista de inteiros."""
        with open(file=arquivo, mode='r', encoding='utf8') as f:
            return [int(linha.split()[0]) for linha in f]

    """Leitura das informações da MP e da Cache a partir dos arquivos"""
    infosMP = ler_arquivo('./MP.txt')
    infosCache = ler_arquivo('./cache.txt')

    """Cálculo do número de blocos na MP"""
    TamBloco = int(infosMP[1] * infosMP[2])
    NumBlocosMP = int((infosMP[0] * 1024) / TamBloco)

    """Cálculo dos bits necessários para bloco e offset"""
    BitsBloco = int(log2(NumBlocosMP))
    Offset = int(log2(TamBloco))

    """Cálculo do número total de bits para o endereço"""
    BitsParaEndereço = BitsBloco + Offset

    """Cálculo do maior endereço válido em decimal na MP"""
    DecimMax = int((infosMP[0] * 1024) - infosMP[2])
    print(f'O maior endereço em decimais válido é {DecimMax} bytes\n')

    """Cálculo do número de linhas na Cache e de conjuntos na Cache"""
    NumLinhasCache = int((infosCache[0] * 1024) / (infosMP[2]))
    NumConjuntos = int(NumLinhasCache / infosCache[1]) # 1 palavra por linha da cache

    """Impressão das informações da MP e da Cache"""
    print(f'\nMP:\nTamanho da MP: {infosMP[0]} KB\nPalavras por bloco da MP: {infosMP[1]}\nTamanho de cada palavra na MP: {infosMP[2]} Bytes\nPalavras por linha da MP: {infosMP[3]}\nTamanho de cada bloco na MP: {TamBloco} Bytes\nNúmero total de blocos na MP: {NumBlocosMP}\n')
    print(f'Bits do bloco: {BitsBloco}\nOffset: {Offset}\n\nEndereços de até {BitsParaEndereço} bits são válidos')
    print(f'\nCache:\nTamanho do cache: {infosCache[0]} KB\nQuantidade de linhas por conjunto: {infosCache[1]}\nQuantidade total de linhas da Cache: {NumLinhasCache}\nQuantidade de conjuntos da Cache: {NumConjuntos}\n')

    """Função interna para converter um endereço de bytes para binário"""
    def endereco_para_binario(endereco_bytes):
        """Converte um endereço de bytes para binário."""
        endereco_binario = bin(endereco_bytes)[2:]
        return endereco_binario.zfill(BitsParaEndereço)

    """Leitura dos números do arquivo de entrada e conversão para binário"""
    numeros = ler_arquivo('./data.txt')
    data = [endereco_para_binario(num) for num in numeros]

    """Impressão dos endereços em binário lidos do arquivo"""
    print('Endereço em binário lido do arquivo:')
    for binario in data: print(binario)

    """Cálculo dos parâmetros w, d, s e tag"""
    w = int(Offset) # bits offset
    print(f'\nw: {w}\n')
    d = int(log2(NumConjuntos)) # bits do índice
    print(f'd: {d}\n')
    s = int(BitsBloco) # bits para o bloco
    print(f's: {s}\n')
    tag = int(s - d) # bits de tag
    print(f'tag: {tag}\n')

    """Inicialização da Cache como uma lista de listas com 'NULL'"""
    t = NumLinhasCache // NumConjuntos
    cache = [['NULL' for _ in range(t)] for _ in range(NumConjuntos)]

    """Variáveis para contagem de acertos, erros e operações"""
    acerto = 0
    errado = 0
    o = 1

    """Loop para simulação de acessos à Cache"""
    for i in range(len(data)):
        x = numeros[i]  # Obter o número inteiro correspondente ao endereço
        bin_x = data[i]  # Obter o binário correspondente ao endereço

        """Extração da TAG"""
        TAG = bin_x[:tag]

        """Extração do D"""
        D = bin_x[tag:-w]

        """Conversão de D para inteiro na base 2"""
        a = int(D, 2) if D else 0

        """Extração do W"""
        W = bin_x[-w:]

        """Extração do S"""
        S = bin_x[:-w]

        """Escolha de um índice aleatório para o conjunto"""
        aux = randint(0, t - 1)

        """Verificação de HIT ou MISS na Cache"""
        if a < len(cache) and TAG in cache[a]:
          acerto += 1
        else:
          cache[a][aux] = TAG
          errado += 1

        """Impressão dos resultados de cada operação"""
        print(f'\n\n-------------------------------------------\nResumo Mapeamento: Associativo por Conjunto\n-------------------------------------------\nEndereço procurado: {x}\nTag: {TAG}\nd: {D}\nw: {W}\ns: {S}\n-------------------------------------------\nTotal de memórias acessadas: {o}\nTotal HIT: {acerto}\nTotal MISS: {errado}')
        taxa_cache_hit = (acerto / o) * 100
        print('Taxa de Cache HIT: {number:.{digits}f}%'.format(number=taxa_cache_hit, digits=2))
        taxa_cache_miss = (errado / o) * 100
        print('Taxa de Cache MISS: {number:.{digits}f}%'.format(number=taxa_cache_miss, digits=2))
        o += 1

def main():
    """Função principal para controle do menu e execução do programa."""
    continuar = True
    while continuar:
        options()
        option = int(input('\nInsira o Valor: '))
        if option == 1:
            MAPR()
        else:
            print("\nMuito Obrigado por Utilizar")
            continuar = False

if __name__ == "__main__":
    main()
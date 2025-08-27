```markdown
# Plano Detalhado para Implementar o Jogo de Contar Cordeirinhos e Cenário Relaxante

## Visão Geral da Funcionalidade
- Desenvolver um jogo simples que exibe um cenário relaxante para ajudar o usuário a dormir, contando "cordeirinhos" que aparecem gradualmente.
- A interface exibirá um fundo com gradiente suave, texto descritivo e botões de controle (Iniciar, Pausar e Resetar).
- Será implementada a contagem automática de cordeirinhos usando intervalos, com tratamento adequado da limpeza do intervalo e erros na renderização das imagens.
- A interface usará tipografia, cores, espaçamentos e layout modernos, sem depender de bibliotecas de ícones externas.

## Arquivos Envolvidos e Dependências
1. **Nova Página do Jogo:**  
   - Arquivo: `src/app/sheep-game/page.tsx`
   - Responsabilidade: Renderizar a página principal do jogo, importando e utilizando o componente do jogo.

2. **Componente Principal do Jogo:**  
   - Arquivo: `src/components/SheepGame.tsx`
   - Responsabilidade: Contém a lógica do jogo, estados para contagem, intervalos, botões de controle e renderização dos cordeirinhos.
   - Detalhe: Usará `useState` e `useEffect` para gerenciar a contagem e o ciclo do jogo. O componente também exibirá imagens de cordeirinhos via tag `<img>` apontando para uma URL de placeholder (usando placehold.co) com fallback para texto caso a imagem não carregue.

3. **Atualizações Globais (se necessário):**  
   - Arquivo: `src/app/globals.css` ou componentes CSS inline via Tailwind.
   - Responsabilidade: Adicionar classes utilitárias para gradientes de fundo, animações suaves e estilos modernos.
   - Nota: Se novas classes CSS customizadas forem necessárias, elas serão adicionadas aqui.

## Instruções Passo a Passo

### 1. Criar a Página do Jogo
- **Arquivo:** `src/app/sheep-game/page.tsx`
- **Passos:**
  - Importar React e o componente `SheepGame` de `src/components/SheepGame.tsx`.
  - Definir um componente de página simples que engloba o jogo.
  - Aplicar um container de largura total com classes Tailwind para centralizar o conteúdo e usar o fundo relaxante (por exemplo, um gradiente suave).

```typescript
// src/app/sheep-game/page.tsx
import React from "react";
import SheepGame from "@/components/SheepGame";

export default function SheepGamePage() {
  return (
    <main className="min-h-screen flex items-center justify-center bg-gradient-to-b from-blue-200 to-purple-200 p-4">
      <SheepGame />
    </main>
  );
}
```

### 2. Desenvolver o Componente do Jogo
- **Arquivo:** `src/components/SheepGame.tsx`
- **Passos:**
  - Importar hooks do React: `useState` e `useEffect`.
  - Inicializar estados:
    - `sheepCount` (número de cordeirinhos contados).
    - `isRunning` (booleano para indicar se o jogo está ativo).
  - Configurar um intervalo com `setInterval` para incrementar a contagem a cada 2 segundos (ou tempo adequado) quando o jogo estiver ativo e garantir que o intervalo seja limpo (clearInterval) no `useEffect` e ao desmontar o componente.
  - Implementar três funções:
    - `startGame`: Ativa o intervalo e define `isRunning` como true.
    - `pauseGame`: Para o intervalo e define `isRunning` como false.
    - `resetGame`: Reseta `sheepCount` para zero e pausa o jogo (limpa o intervalo).
  - Renderizar:
    - Um título explicativo: “Contando Cordeirinhos Para Dormir”.
    - Um display grande do número de cordeirinhos.
    - Uma área de visualização onde cada novo cordeirinho é exibido com uma imagem de placeholder.
      - Usar uma tag `<img>` com `src` definido como:  
        `https://placehold.co/100x100?text=Cordeirinho+Ilustrado+Relaxante`
      - Adicionar atributos `alt` com descrição detalhada e um `onerror` para fallback (possivelmente para um elemento `<span>` ou uma mensagem).
    - Botões "Iniciar", "Pausar" e "Resetar" com estados apropriados (ex: desabilitar "Iniciar" se já estiver rodando).
  - Tratar erros: Garantir que, se a renderização da imagem falhar, um fallback de texto seja exibido. Adicionar try/catch se necessário dentro de funções assíncronas.

```typescript
// src/components/SheepGame.tsx
import React, { useEffect, useState, useRef } from "react";

export default function SheepGame() {
  const [sheepCount, setSheepCount] = useState(0);
  const [isRunning, setIsRunning] = useState(false);
  const intervalRef = useRef<NodeJS.Timeout | null>(null);

  // Iniciar o contador de cordeirinhos
  const startGame = () => {
    if (!isRunning) {
      setIsRunning(true);
    }
  };

  // Pausar o jogo e limpar o intervalo
  const pauseGame = () => {
    setIsRunning(false);
    if (intervalRef.current) {
      clearInterval(intervalRef.current);
      intervalRef.current = null;
    }
  };

  // Resetar o contador e pausar o jogo
  const resetGame = () => {
    pauseGame();
    setSheepCount(0);
  };

  useEffect(() => {
    if (isRunning) {
      // Configura o intervalo a cada 2000ms (2s)
      intervalRef.current = setInterval(() => {
        setSheepCount((prevCount) => prevCount + 1);
      }, 2000);
    }
    return () => {
      // Limpa o intervalo ao desmontar ou pausar o jogo
      if (intervalRef.current) {
        clearInterval(intervalRef.current);
        intervalRef.current = null;
      }
    };
  }, [isRunning]);

  // Renderização do componente para cada cordeirinho
  const renderSheep = () => {
    // Gerar um array com base em sheepCount; cada elemento exibe uma imagem ou um fallback de texto
    return Array.from({ length: sheepCount }, (_, index) => (
      <div key={index} className="m-2">
        <img
          src="https://placehold.co/100x100?text=Cordeirinho+Ilustrado+Relaxante"
          alt="Ilustração detalhada de um cordeirinho fofo com cores suaves, estilo minimalista para um ambiente relaxante"
          onError={(e) => {
            const target = e.currentTarget;
            target.onerror = null;
            target.style.display = "none";
          }}
          className="w-24 h-24 object-cover rounded"
        />
      </div>
    ));
  };

  return (
    <div className="bg-white bg-opacity-80 backdrop-blur-md p-6 rounded-lg shadow-lg max-w-md w-full">
      <h1 className="text-2xl font-bold text-center mb-4">Contando Cordeirinhos Para Dormir</h1>
      <div className="text-center mb-4">
        <span className="text-6xl font-extrabold">{sheepCount}</span>
      </div>
      <div className="flex flex-wrap justify-center mb-6">
        {sheepCount > 0 ? renderSheep() : <p className="text-gray-600">Pressione "Iniciar" para começar.</p>}
      </div>
      <div className="flex space-x-4 justify-center">
        <button
          onClick={startGame}
          disabled={isRunning}
          className="px-4 py-2 bg-green-500 text-white rounded hover:bg-green-600 transition disabled:opacity-50"
        >
          Iniciar
        </button>
        <button
          onClick={pauseGame}
          disabled={!isRunning}
          className="px-4 py-2 bg-yellow-500 text-white rounded hover:bg-yellow-600 transition disabled:opacity-50"
        >
          Pausar
        </button>
        <button
          onClick={resetGame}
          className="px-4 py-2 bg-red-500 text-white rounded hover:bg-red-600 transition"
        >
          Resetar
        </button>
      </div>
    </div>
  );
}
```

### 3. Estilização e Layout Moderno
- Utilizar o Tailwind CSS para:
  - Definir um fundo relaxante no container principal da página.
  - Aplicar sombras suaves, bordas arredondadas e espaçamentos confortáveis no componente do jogo.
  - Garantir que a tipografia seja clara e legível, com destaque para o contador dos cordeirinhos.
- Caso seja necessário, adicionar regras CSS customizadas em `src/app/globals.css`.

### 4. Tratamento de Erros e Boas Práticas
- **Intervalo e Limpeza:**  
  - Certificar de limpar o intervalo criado no `useEffect` sempre que o jogo for pausado ou o componente desmontar.
- **Fallback nas Imagens:**  
  - Adicionar um `onError` na tag `<img>` para ocultar a imagem e, se desejado, exibir um fallback de texto.
- **Acessibilidade:**  
  - Utilizar atributos `alt` detalhados para todas as imagens.
  - Garantir que botões têm textos e estados de desativação visual (usando `disabled:opacity-50`).

### 5. Testes e Validação
- Executar a aplicação com `npm run dev` ou similar para garantir que:
  - A página `sheep-game` é acessível através da URL `/sheep-game`.
  - O botão “Iniciar” inicia a contagem a cada 2 segundos.
  - Os botões “Pausar” e “Resetar” funcionam corretamente.
  - A renderização das imagens de cordeirinhos e os fallbacks para erros funcionem adequadamente.
- Validar a responsividade e a experiência de usuário em diferentes tamanhos de tela.

## Resumo
- Criamos um novo arquivo de página em `src/app/sheep-game/page.tsx` que exibe o jogo centralizado com fundo gradiente.  
- Desenvolvemos o componente `SheepGame` em `src/components/SheepGame.tsx` com lógica de contagem, intervalos e renderização de imagens de cordeirinhos.  
- Utilizamos Tailwind CSS para garantir uma interface moderna e relaxante com tipografia, cores suaves e espaçamentos adequados.  
- Implementamos tratamento de erros para imagem via atributo `onError` e garantimos a limpeza do intervalo com `useEffect`.  
- Os botões de controle (Iniciar, Pausar, Resetar) foram implementados com estados de desativação para melhorar a UX.  
- A aplicação foi planejada para ser escalável e integrada ao ecossistema Next.js existente, com atenção a boas práticas de React e acessibilidade.

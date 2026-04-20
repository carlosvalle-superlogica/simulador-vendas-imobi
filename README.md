import React, { useState } from 'react';

export default function SimuladorCloser() {
  // Estados do Simulador
  const [produto, setProduto] = useState('CRM'); // CRM, ERP, AMBOS
  const [regiao, setRegiao] = useState('SP'); // SP, SUL_SUDESTE, CENTRO_OESTE, NORTE_NORDESTE
  const [planoAtende, setPlanoAtende] = useState(100);

  // Lógica de Preço Base do Atende (Exemplo simplificado baseado na proporção)
  // Plano 100 = R$ 350 | Plano 1000 = R$ 1450
  const calcularPrecoBaseAtende = (plano) => {
    if (plano <= 100) return 350;
    if (plano === 1000) return 1450;
    // Cálculo proporcional simples para os valores intermediários
    return 350 + ((plano - 100) * (1100 / 900)); 
  };

  const precoBase = calcularPrecoBaseAtende(planoAtende);

  // Lógica de Descontos baseada no Produto e Região
  const calcularDesconto = () => {
    const regras = {
      CRM: {
        SP: 0.20,
        SUL_SUDESTE: 0.40,
        CENTRO_OESTE: 0.40,
        NORTE_NORDESTE: 0.70,
      },
      ERP: {
        SP: 0.00,
        SUL_SUDESTE: 0.80,
        CENTRO_OESTE: 0.90,
        NORTE_NORDESTE: 1.00,
      },
      AMBOS: { // CRM + ERP (Pacote Completo - Mais agressivo)
        SP: 0.50, // Exemplo: 50% em SP se levar os dois
        SUL_SUDESTE: 0.90,
        CENTRO_OESTE: 1.00,
        NORTE_NORDESTE: 1.00,
      }
    };

    return regras[produto][regiao] || 0;
  };

  const percentualDesconto = calcularDesconto();
  const valorDesconto = precoBase * percentualDesconto;
  const precoFinal = precoBase - valorDesconto;

  return (
    <div className="min-h-screen bg-gray-50 flex items-center justify-center p-4 font-sans">
      <div className="bg-white max-w-4xl w-full rounded-2xl shadow-xl overflow-hidden flex flex-col md:flex-row">
        
        {/* LADO ESQUERDO: Controles (O que o Closer opera) */}
        <div className="w-full md:w-3/5 p-8 border-r border-gray-100">
          <h1 className="text-2xl font-bold text-gray-800 mb-6">Simulador de Proposta</h1>

          {/* Seleção de Produto */}
          <div className="mb-8">
            <label className="block text-sm font-semibold text-gray-600 mb-3">1. Produto Âncora</label>
            <div className="grid grid-cols-3 gap-3">
              {['CRM', 'ERP', 'AMBOS'].map((item) => (
                <button
                  key={item}
                  onClick={() => setProduto(item)}
                  className={`py-3 px-4 rounded-lg font-medium transition-all ${
                    produto === item
                      ? 'bg-blue-600 text-white shadow-md'
                      : 'bg-gray-100 text-gray-600 hover:bg-gray-200'
                  }`}
                >
                  {item === 'AMBOS' ? 'CRM + ERP' : item}
                </button>
              ))}
            </div>
          </div>

          {/* Seleção de Região */}
          <div className="mb-8">
            <label className="block text-sm font-semibold text-gray-600 mb-3">2. Região do Cliente</label>
            <div className="grid grid-cols-2 gap-3">
              {[
                { id: 'SP', label: 'Cidade SP' },
                { id: 'SUL_SUDESTE', label: 'Sul / Sudeste' },
                { id: 'CENTRO_OESTE', label: 'Centro-Oeste' },
                { id: 'NORTE_NORDESTE', label: 'Norte / Nordeste' },
              ].map((reg) => (
                <button
                  key={reg.id}
                  onClick={() => setRegiao(reg.id)}
                  className={`py-2 px-3 rounded-lg text-sm font-medium transition-all ${
                    regiao === reg.id
                      ? 'bg-blue-600 text-white shadow-md'
                      : 'bg-gray-100 text-gray-600 hover:bg-gray-200'
                  }`}
                >
                  {reg.label}
                </button>
              ))}
            </div>
          </div>

          {/* Slider do Plano Atende */}
          <div className="mb-4">
            <label className="block text-sm font-semibold text-gray-600 mb-3">
              3. Plano Atende (Volume)
            </label>
            <input
              type="range"
              min="100"
              max="1000"
              step="100"
              value={planoAtende}
              onChange={(e) => setPlanoAtende(Number(e.target.value))}
              className="w-full h-2 bg-gray-200 rounded-lg appearance-none cursor-pointer accent-blue-600"
            />
            <div className="flex justify-between text-xs text-gray-500 mt-2 font-medium">
              <span>Plano 100</span>
              <span className="text-blue-600 font-bold text-base">Plano {planoAtende}</span>
              <span>Plano 1000</span>
            </div>
          </div>
        </div>

        {/* LADO DIREITO: Resultado (O Wow Factor) */}
        <div className="w-full md:w-2/5 bg-gray-50 p-8 flex flex-col justify-center">
          <div className="bg-white p-6 rounded-xl shadow-sm border border-gray-100 text-center">
            <h2 className="text-sm font-bold text-gray-500 uppercase tracking-wider mb-2">Resumo Atende</h2>
            
            <div className="mb-4">
              <span className="text-gray-400 line-through text-lg">
                R$ {precoBase.toLocaleString('pt-BR', { minimumFractionDigits: 2, maximumFractionDigits: 2 })}
              </span>
            </div>

            <div className="mb-6">
              <div className="text-5xl font-black text-gray-800">
                R$ {precoFinal.toLocaleString('pt-BR', { minimumFractionDigits: 2, maximumFractionDigits: 2 })}
                <span className="text-lg text-gray-500 font-normal">/mês</span>
              </div>
            </div>

            {percentualDesconto > 0 && (
              <div className="inline-block bg-green-100 text-green-700 px-4 py-2 rounded-full font-bold text-sm animate-pulse">
                🔥 Você destravou {percentualDesconto * 100}% OFF!
              </div>
            )}
            
            {percentualDesconto === 1 && (
              <p className="text-green-600 font-bold mt-4 text-sm">
                Nesta configuração, o Atende sai de GRAÇA!
              </p>
            )}
          </div>

          <button className="mt-6 w-full bg-green-500 hover:bg-green-600 text-white font-bold py-4 rounded-xl shadow-lg transition-transform transform hover:scale-105">
            Gerar Proposta Oficial
          </button>
        </div>

      </div>
    </div>
  );
}

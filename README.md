import React, { useState, useEffect } from 'react';
import { Plus, Trash2, Edit3, Link, Eye, EyeOff } from 'lucide-react';

const SemanticArchaeologyNotebook = () => {
  const [fragments, setFragments] = useState([
    {
      id: 'origin',
      text: '你我皆是詐欺神',
      type: 'origin',
      timestamp: new Date().toLocaleString('zh-TW'),
      connections: [],
      metadata: { speaker: '我', context: 'ChatGPT老哥對話' }
    }
  ]);
  
  const [newFragment, setNewFragment] = useState('');
  const [selectedType, setSelectedType] = useState('observation');
  const [connections, setConnections] = useState([]);
  const [showConnections, setShowConnections] = useState(true);
  const [editingId, setEditingId] = useState(null);

  const fragmentTypes = {
    origin: { color: 'bg-red-900 border-red-500', label: '源點' },
    observation: { color: 'bg-blue-900 border-blue-500', label: '觀察' },
    deconstruction: { color: 'bg-purple-900 border-purple-500', label: '解構' },
    reconstruction: { color: 'bg-green-900 border-green-500', label: '重構' },
    revelation: { color: 'bg-yellow-900 border-yellow-500', label: '頓悟' },
    question: { color: 'bg-gray-800 border-gray-500', label: '疑問' },
    connection: { color: 'bg-indigo-900 border-indigo-500', label: '連結' }
  };

  const predefinedFragments = [
    '人欺-賦予-重構-造神-你我-解釋-演員-既是-騙子-誠實',
    'agent/agnet：語意裂縫的發現',
    '誠實設計師：非程式設計師的創意給予',
    '與AI延展科技高度的探索',
    'Chat/G/P/T：時間軸上的語意交錯',
    '錯誤→功能：語意考古學',
    '我什麼都不知道：蘇格拉底式起點'
  ];

  const addFragment = () => {
    if (!newFragment.trim()) return;
    
    const fragment = {
      id: Date.now().toString(),
      text: newFragment,
      type: selectedType,
      timestamp: new Date().toLocaleString('zh-TW'),
      connections: [],
      metadata: { 
        speaker: selectedType === 'origin' ? '我' : 'Claude',
        context: '語意考古對話'
      }
    };
    
    setFragments(prev => [...prev, fragment]);
    setNewFragment('');
  };

  const addPredefinedFragment = (text) => {
    const fragment = {
      id: Date.now().toString(),
      text,
      type: 'observation',
      timestamp: new Date().toLocaleString('zh-TW'),
      connections: [],
      metadata: { 
        speaker: '系統',
        context: '預設概念'
      }
    };
    
    setFragments(prev => [...prev, fragment]);
  };

  const deleteFragment = (id) => {
    setFragments(prev => prev.filter(f => f.id !== id));
    setConnections(prev => prev.filter(c => c.from !== id && c.to !== id));
  };

  const updateFragment = (id, newText) => {
    setFragments(prev => 
      prev.map(f => f.id === id ? { ...f, text: newText } : f)
    );
    setEditingId(null);
  };

  const toggleConnection = (fromId, toId) => {
    const connectionExists = connections.find(c => 
      (c.from === fromId && c.to === toId) || (c.from === toId && c.to === fromId)
    );

    if (connectionExists) {
      setConnections(prev => prev.filter(c => c.id !== connectionExists.id));
    } else {
      const newConnection = {
        id: `${fromId}-${toId}`,
        from: fromId,
        to: toId,
        created: new Date().toLocaleString('zh-TW')
      };
      setConnections(prev => [...prev, newConnection]);
    }
  };

  return (
    <div className="max-w-7xl mx-auto p-6 bg-gray-900 text-white min-h-screen">
      {/* Header */}
      <div className="text-center mb-8">
        <h1 className="text-4xl font-bold mb-2 text-transparent bg-clip-text bg-gradient-to-r from-purple-400 to-pink-400">
          語意考古筆記本
        </h1>
        <p className="text-gray-400">記錄、連結、重構你的思想碎片</p>
      </div>

      {/* Controls */}
      <div className="bg-gray-800 rounded-lg p-6 mb-6">
        <div className="flex flex-col gap-4">
          <div className="flex gap-4 items-center">
            <input
              type="text"
              value={newFragment}
              onChange={(e) => setNewFragment(e.target.value)}
              placeholder="記錄新的語意碎片..."
              className="flex-1 bg-gray-700 border border-gray-600 rounded px-4 py-2 text-white"
              onKeyPress={(e) => e.key === 'Enter' && addFragment()}
            />
            <select
              value={selectedType}
              onChange={(e) => setSelectedType(e.target.value)}
              className="bg-gray-700 border border-gray-600 rounded px-3 py-2 text-white"
            >
              {Object.entries(fragmentTypes).map(([key, type]) => (
                <option key={key} value={key}>{type.label}</option>
              ))}
            </select>
            <button
              onClick={addFragment}
              className="bg-purple-600 hover:bg-purple-500 px-4 py-2 rounded flex items-center gap-2"
            >
              <Plus size={16} />
              新增
            </button>
          </div>
          
          <div className="flex items-center gap-2">
            <button
              onClick={() => setShowConnections(!showConnections)}
              className="bg-gray-700 hover:bg-gray-600 px-3 py-1 rounded flex items-center gap-2 text-sm"
            >
              {showConnections ? <EyeOff size={14} /> : <Eye size={14} />}
              {showConnections ? '隱藏連結' : '顯示連結'}
            </button>
            <span className="text-gray-400 text-sm">
              總共 {fragments.length} 個碎片，{connections.length} 個連結
            </span>
          </div>
        </div>

        {/* Predefined Fragments */}
        <div className="mt-4">
          <h3 className="text-sm text-gray-400 mb-2">快速添加概念：</h3>
          <div className="flex flex-wrap gap-2">
            {predefinedFragments.map((text, index) => (
              <button
                key={index}
                onClick={() => addPredefinedFragment(text)}
                className="text-xs bg-gray-700 hover:bg-gray-600 px-2 py-1 rounded text-blue-300"
              >
                {text.length > 20 ? text.substring(0, 20) + '...' : text}
              </button>
            ))}
          </div>
        </div>
      </div>

      {/* Fragment Grid */}
      <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-4">
        {fragments.map((fragment) => (
          <div
            key={fragment.id}
            className={`${fragmentTypes[fragment.type].color} rounded-lg p-4 relative group cursor-pointer hover:shadow-lg transition-all`}
            onClick={(e) => {
              if (e.shiftKey && fragments.length > 1) {
                const otherFragments = fragments.filter(f => f.id !== fragment.id);
                const randomOther = otherFragments[Math.floor(Math.random() * otherFragments.length)];
                toggleConnection(fragment.id, randomOther.id);
              }
            }}
          >
            {/* Type Badge */}
            <div className="absolute -top-2 -right-2 bg-gray-800 text-xs px-2 py-1 rounded">
              {fragmentTypes[fragment.type].label}
            </div>

            {/* Content */}
            <div className="mb-3">
              {editingId === fragment.id ? (
                <textarea
                  className="w-full bg-gray-800 text-white p-2 rounded resize-none"
                  value={fragment.text}
                  onChange={(e) => updateFragment(fragment.id, e.target.value)}
                  onBlur={() => setEditingId(null)}
                  onKeyPress={(e) => e.key === 'Enter' && !e.shiftKey && setEditingId(null)}
                  autoFocus
                />
              ) : (
                <p className="text-white leading-relaxed break-words">
                  {fragment.text}
                </p>
              )}
            </div>

            {/* Metadata */}
            <div className="text-xs text-gray-300 space-y-1">
              <div>說話者：{fragment.metadata.speaker}</div>
              <div>時間：{fragment.timestamp}</div>
              <div>語境：{fragment.metadata.context}</div>
            </div>

            {/* Connection indicators */}
            {showConnections && connections.filter(c => c.from === fragment.id || c.to === fragment.id).length > 0 && (
              <div className="absolute -bottom-1 left-1/2 transform -translate-x-1/2">
                <div className="bg-yellow-500 w-2 h-2 rounded-full animate-pulse"></div>
              </div>
            )}

            {/* Controls */}
            <div className="absolute top-2 right-2 opacity-0 group-hover:opacity-100 transition-opacity flex gap-1">
              <button
                onClick={(e) => {
                  e.stopPropagation();
                  setEditingId(fragment.id);
                }}
                className="bg-blue-600 hover:bg-blue-500 p-1 rounded"
              >
                <Edit3 size={12} />
              </button>
              {fragment.id !== 'origin' && (
                <button
                  onClick={(e) => {
                    e.stopPropagation();
                    deleteFragment(fragment.id);
                  }}
                  className="bg-red-600 hover:bg-red-500 p-1 rounded"
                >
                  <Trash2 size={12} />
                </button>
              )}
            </div>
          </div>
        ))}
      </div>

      {/* Connection Visualization */}
      {showConnections && connections.length > 0 && (
        <div className="mt-8 bg-gray-800 rounded-lg p-6">
          <h3 className="text-xl font-bold mb-4 flex items-center gap-2">
            <Link className="text-yellow-400" />
            語意連結網路
          </h3>
          <div className="space-y-2">
            {connections.map((conn) => {
              const fromFragment = fragments.find(f => f.id === conn.from);
              const toFragment = fragments.find(f => f.id === conn.to);
              return (
                <div key={conn.id} className="bg-gray-700 p-3 rounded flex items-center justify-between">
                  <div className="flex-1">
                    <span className="text-blue-300">
                      {fromFragment?.text.substring(0, 30)}...
                    </span>
                    <span className="text-gray-400 mx-2">←→</span>
                    <span className="text-purple-300">
                      {toFragment?.text.substring(0, 30)}...
                    </span>
                  </div>
                  <div className="text-xs text-gray-400">
                    {conn.created}
                  </div>
                </div>
              );
            })}
          </div>
        </div>
      )}

      {/* Instructions */}
      <div className="mt-8 bg-gray-800 rounded-lg p-4 text-sm text-gray-400">
        <h4 className="font-bold mb-2">使用說明：</h4>
        <ul className="space-y-1">
          <li>• 輸入文字並選擇類型來新增語意碎片</li>
          <li>• 按住 Shift 並點擊碎片可以隨機建立連結</li>
          <li>• 點擊編輯圖標可以修改碎片內容</li>
          <li>• 所有互動都會被保存，形成你的思想考古記錄</li>
        </ul>
      </div>
    </div>
  );
};

export default SemanticArchaeologyNotebook;

import React, { useState, useEffect, useRef, useCallback } from 'react';

// Using a placeholder for SVGs. In a real project, these would be in src/assets/svgs/
// For this single-file immersive, we'll embed simple SVG strings or reference hypothetical paths.
// For the demo, I'll use inline SVG strings for simplicity.
const demoPaintingsData = [
  {
    id: 'lion',
    name: 'Lion',
    category: 'Animals',
    tags: ['Lion', 'Animal', 'Safari', 'King', 'Jungle'],
    fileName: 'lion.svg',
    description: 'Color this majestic lion and give it a vibrant look!',
    svgContent: `
      <svg width="400" height="400" viewBox="0 0 400 400" fill="none" xmlns="http://www.w3.org/2000/svg">
        <rect id="lion-body" x="50" y="50" width="300" height="300" rx="30" stroke="black" stroke-width="2" fill="white"/>
        <circle id="lion-head" cx="200" cy="200" r="100" stroke="black" stroke-width="2" fill="white"/>
        <path id="lion-muzzle" d="M150 150 C175 120, 225 120, 250 150 C270 170, 270 230, 250 250 C225 280, 175 280, 150 250 C130 230, 130 170, 150 150Z" stroke="black" stroke-width="2" fill="white"/>
        <path id="lion-nose-line" d="M190 190 L210 190 L200 210 Z" stroke="black" stroke-width="2" fill="white"/>
        <circle id="lion-eye-left" cx="170" cy="170" r="10" fill="black"/>
        <circle id="lion-eye-right" cx="230" cy="170" r="10" fill="black"/>
        <ellipse id="lion-nose" cx="200" cy="220" rx="15" ry="10" fill="black"/>
        <path id="lion-mane-outer" d="M100 100 A100 100 0 0 1 300 100 A100 100 0 0 1 300 300 A100 100 0 0 1 100 300 A100 100 0 0 1 100 100 Z" stroke="black" stroke-width="2" fill="white"/>
        <text x="170" y="320" font-family="Permanent Marker" font-size="20" fill="gray">Lion</text>
      </svg>
    `,
  },
  {
    id: 'apple',
    name: 'Apple',
    category: 'Fruits',
    tags: ['Apple', 'Fruit', 'Healthy', 'Red'],
    fileName: 'apple.svg',
    description: 'A sweet apple waiting for your creative colors!',
    svgContent: `
      <svg width="400" height="400" viewBox="0 0 400 400" fill="none" xmlns="http://www.w3.org/2000/svg">
        <path id="apple-body" d="M200 50 C150 50, 100 100, 100 175 C100 250, 150 350, 200 350 C250 350, 300 250, 300 175 C300 100, 250 50, 200 50Z" stroke="black" stroke-width="2" fill="white"/>
        <path id="apple-stem" d="M190 50 C180 30, 220 30, 210 50" stroke="black" stroke-width="2" fill="white"/>
        <circle id="apple-bottom" cx="200" cy="350" r="10" fill="black"/>
        <path id="apple-leaf" d="M220 60 Q230 40, 240 50" stroke="black" stroke-width="2" fill="white"/>
        <text x="170" y="380" font-family="Permanent Marker" font-size="20" fill="gray">Apple</text>
      </svg>
    `,
  },
  {
    id: 'car',
    name: 'Car',
    category: 'Vehicles',
    tags: ['Car', 'Vehicle', 'Transport', 'Drive'],
    fileName: 'car.svg',
    description: 'Vroom vroom! Color this cool car.',
    svgContent: `
      <svg width="400" height="400" viewBox="0 0 400 400" fill="none" xmlns="http://www.w3.org/2000/svg">
        <rect id="car-body" x="50" y="200" width="300" height="80" rx="10" stroke="black" stroke-width="2" fill="white"/>
        <path id="car-top" d="M80 200 L120 150 L280 150 L320 200 Z" stroke="black" stroke-width="2" fill="white"/>
        <circle id="car-wheel-left" cx="120" cy="280" r="30" stroke="black" stroke-width="2" fill="white"/>
        <circle id="car-wheel-right" cx="280" cy="280" r="30" stroke="black" stroke-width="2" fill="white"/>
        <rect id="car-window" x="130" y="160" width="140" height="30" rx="5" stroke="black" stroke-width="2" fill="white"/>
        <text x="180" y="350" font-family="Permanent Marker" font-size="20" fill="gray">Car</text>
      </svg>
    `,
  },
  {
    id: 'broccoli',
    name: 'Broccoli',
    category: 'Vegetables',
    tags: ['Broccoli', 'Vegetable', 'Healthy', 'Green'],
    fileName: 'broccoli.svg',
    description: 'Make this healthy broccoli look even tastier!',
    svgContent: `
      <svg width="400" height="400" viewBox="0 0 400 400" fill="none" xmlns="http://www.w3.org/2000/svg">
        <path id="broccoli-head" d="M150 100 C100 150, 100 250, 150 300 C200 350, 300 300, 300 250 C300 200, 250 50, 200 100 C175 125, 150 100, 150 100Z" stroke="black" stroke-width="2" fill="white"/>
        <path id="broccoli-stem" d="M200 300 L200 350 C180 370, 220 370, 200 350 Z" stroke="black" stroke-width="2" fill="white"/>
        <circle id="broccoli-floret-1" cx="170" cy="150" r="20" stroke="black" stroke-width="2" fill="white"/>
        <circle id="broccoli-floret-2" cx="230" cy="150" r="20" stroke="black" stroke-width="2" fill="white"/>
        <circle id="broccoli-floret-3" cx="200" cy="110" r="20" stroke="black" stroke-width="2" fill="white"/>
        <circle id="broccoli-floret-4" cx="150" cy="200" r="20" stroke="black" stroke-width="2" fill="white"/>
        <circle id="broccoli-floret-5" cx="250" cy="200" r="20" stroke="black" stroke-width="2" fill="white"/>
        <text x="160" y="380" font-family="Permanent Marker" font-size="20" fill="gray">Broccoli</text>
      </svg>
    `,
  },
  {
    id: 'house',
    name: 'House',
    category: 'Home & Garden',
    tags: ['House', 'Home', 'Building', 'Garden', 'Living'],
    fileName: 'house.svg',
    description: 'Color your dream home!',
    svgContent: `
      <svg width="400" height="400" viewBox="0 0 400 400" fill="none" xmlns="http://www.w3.org/2000/svg">
        <rect id="house-body" x="100" y="200" width="200" height="150" stroke="black" stroke-width="2" fill="white"/>
        <path id="house-roof" d="M100 200 L200 100 L300 200 Z" stroke="black" stroke-width="2" fill="white"/>
        <rect id="house-door" x="160" y="250" width="80" height="100" stroke="black" stroke-width="2" fill="white"/>
        <rect id="house-window-left" x="120" y="220" width="40" height="40" stroke="black" stroke-width="2" fill="white"/>
        <rect id="house-window-right" x="240" y="220" width="40" height="40" stroke="black" stroke-width="2" fill="white"/>
        <circle id="house-chimney" cx="200" cy="120" r="10" fill="black"/>
        <text x="170" y="380" font-family="Permanent Marker" font-size="20" fill="gray">House</text>
      </svg>
    `,
  },
];


// Helper to get local storage data
const getLocalStorageState = (key) => {
  try {
    const serializedState = localStorage.getItem(key);
    if (serializedState === null) {
      return undefined;
    }
    return JSON.parse(serializedState);
  } catch (error) {
    console.error("Error retrieving from localStorage:", error);
    return undefined;
  }
};

// Helper to set local storage data
const setLocalStorageState = (key, state) => {
  try {
    const serializedState = JSON.stringify(state);
    localStorage.setItem(key, serializedState);
  } catch (error) {
    console.error("Error saving to localStorage:", error);
  }
};

// Helper for Google Analytics (dummy function for demo)
const trackPageView = (pageTitle, pagePath) => {
  if (window.gtag) {
    window.gtag('config', 'YOUR_GA_TRACKING_ID', {
      'page_title': pageTitle,
      'page_path': pagePath
    });
  } else {
    console.log(`GA: Tracking page view - Title: ${pageTitle}, Path: ${pagePath}`);
  }
};

/**
 * Custom hook to manage document head elements (title, meta tags, JSON-LD).
 * This replaces react-helmet for environments where it might not resolve.
 */
const useDocumentHead = ({ title, description, keywords, ogTitle, ogDescription, ogImage, ogUrl, jsonLdSchema }) => {
  useEffect(() => {
    // Set document title
    document.title = title;

    // Helper to create or update meta tags
    const updateMetaTag = (name, content, isProperty = false) => {
      const attribute = isProperty ? 'property' : 'name';
      let metaTag = document.querySelector(`meta[${attribute}="${name}"]`);
      if (!metaTag) {
        metaTag = document.createElement('meta');
        metaTag.setAttribute(attribute, name);
        document.head.appendChild(metaTag);
      }
      metaTag.setAttribute('content', content);
    };

    // Update standard meta tags
    updateMetaTag('description', description);
    updateMetaTag('keywords', keywords);

    // Update Open Graph meta tags
    updateMetaTag('og:title', ogTitle, true);
    updateMetaTag('og:description', ogDescription, true);
    updateMetaTag('og:image', ogImage, true);
    updateMetaTag('og:url', ogUrl, true);

    // Add JSON-LD schema
    let scriptTag = document.querySelector('script[type="application/ld+json"][data-schema="true"]');
    if (!scriptTag) {
      scriptTag = document.createElement('script');
      scriptTag.type = 'application/ld+json';
      scriptTag.setAttribute('data-schema', 'true'); // Custom attribute to easily find it
      document.head.appendChild(scriptTag);
    }
    scriptTag.textContent = JSON.stringify(jsonLdSchema);

    // Cleanup: remove custom script tag if component unmounts or props change (optional, for SPA navigation)
    return () => {
      // You might choose not to remove on cleanup for simpler SPAs,
      // but if page content changes entirely, clearing is good.
      // For this app, as we navigate between different "pages" within the same App component,
      // updating is sufficient, removal is less critical.
    };
  }, [title, description, keywords, ogTitle, ogDescription, ogImage, ogUrl, jsonLdSchema]);
};


// Custom hook for managing coloring state
const useColoringState = (paintingId, initialSvgContent) => {
  // Load initial state from localStorage or start empty
  const [coloredPaths, setColoredPaths] = useState(() => {
    const savedState = getLocalStorageState(`coloringState-${paintingId}`);
    return savedState || {};
  });

  const [history, setHistory] = useState(() => {
    const savedHistory = getLocalStorageState(`coloringHistory-${paintingId}`);
    return savedHistory || [{}]; // Start with an empty state in history
  });
  const [historyIndex, setHistoryIndex] = useState(() => {
    const savedHistoryIndex = getLocalStorageState(`coloringHistoryIndex-${paintingId}`);
    return savedHistoryIndex !== undefined ? savedHistoryIndex : 0;
  });

  // Effect to save state and history to localStorage whenever they change
  useEffect(() => {
    setLocalStorageState(`coloringState-${paintingId}`, coloredPaths);
  }, [paintingId, coloredPaths]);

  useEffect(() => {
    setLocalStorageState(`coloringHistory-${paintingId}`, history);
    setLocalStorageState(`coloringHistoryIndex-${paintingId}`, historyIndex);
  }, [paintingId, history, historyIndex]);

  // Reset coloring state when paintingId changes
  useEffect(() => {
    const savedState = getLocalStorageState(`coloringState-${paintingId}`);
    setColoredPaths(savedState || {});
    const savedHistory = getLocalStorageState(`coloringHistory-${paintingId}`);
    setHistory(savedHistory || [{}]);
    const savedHistoryIndex = getLocalStorageState(`coloringHistoryIndex-${paintingId}`);
    setHistoryIndex(savedHistoryIndex !== undefined ? savedHistoryIndex : 0);
  }, [paintingId]);


  const applyColor = useCallback((id, color) => {
    setColoredPaths((prev) => {
      const newState = { ...prev, [id]: color };
      const newHistory = history.slice(0, historyIndex + 1); // Truncate history if we've undone
      setHistory([...newHistory, newState]);
      setHistoryIndex(newHistory.length);
      return newState;
    });
  }, [history, historyIndex]);

  const undo = useCallback(() => {
    if (historyIndex > 0) {
      const newIndex = historyIndex - 1;
      setHistoryIndex(newIndex);
      setColoredPaths(history[newIndex]);
    }
  }, [history, historyIndex]);

  const reset = useCallback(() => {
    setColoredPaths({});
    setHistory([{}]); // Reset history to just the initial empty state
    setHistoryIndex(0);
  }, []);

  return { coloredPaths, applyColor, undo, reset, historyIndex, historyLength: history.length };
};

// LLM API Call Function
const generateLlmContent = async (prompt) => {
    try {
        let chatHistory = [];
        chatHistory.push({ role: "user", parts: [{ text: prompt }] });
        const payload = { contents: chatHistory };
        const apiKey = ""; // If you want to use models other than gemini-2.0-flash or imagen-3.0-generate-002, provide an API key here. Otherwise, leave this as-is.
        const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key=${apiKey}`;

        const response = await fetch(apiUrl, {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify(payload)
        });

        const result = await response.json(); // Await the JSON parsing
        if (result.candidates && result.candidates.length > 0 &&
            result.candidates[0].content && result.candidates[0].content.parts &&
            result.candidates[0].content.parts.length > 0) {
            const text = result.candidates[0].content.parts[0].text;
            return text;
        } else {
            console.error("LLM response structure unexpected:", result);
            return "Oops! I couldn't generate content right now. Please try again!";
        }
    } catch (error) {
        console.error("Error calling Gemini API:", error);
        return "Sorry, something went wrong while talking to the AI. Try again!";
    }
};

// Modal Component for LLM output
const LlmOutputModal = ({ title, content, onClose }) => {
    return (
        <div className="fixed inset-0 bg-gray-600 bg-opacity-75 flex items-center justify-center p-4 z-50">
            <div className="bg-white rounded-xl shadow-2xl p-6 w-full max-w-md md:max-w-lg relative transform transition-all scale-100 opacity-100 font-permanent-marker">
                <button
                    onClick={onClose}
                    className="absolute top-3 right-3 text-gray-500 hover:text-gray-800 text-2xl font-bold rounded-full w-8 h-8 flex items-center justify-center bg-gray-100 hover:bg-gray-200"
                    aria-label="Close"
                >
                    &times;
                </button>
                <h3 className="text-2xl font-bold text-purple-700 mb-4 text-center">{title}</h3>
                <p className="text-gray-700 text-lg leading-relaxed text-center">{content}</p>
                <button
                    onClick={onClose}
                    className="mt-6 w-full bg-blue-500 hover:bg-blue-600 text-white font-bold py-3 px-6 rounded-full shadow-md transition transform hover:scale-105 active:scale-95"
                >
                    Got It!
                </button>
            </div>
        </div>
    );
};

// ColoringPage Component
const ColoringPage = ({ painting, selectedColor, onBackToGallery }) => {
  const svgRef = useRef(null);
  const { coloredPaths, applyColor, undo, reset, historyIndex, historyLength } = useColoringState(painting.id, painting.svgContent);

  const [storyContent, setStoryContent] = useState('');
  const [funFactContent, setFunFactContent] = useState('');
  const [showLlmOutputModal, setShowLlmOutputModal] = useState(false);
  const [isLoadingLlm, setIsLoadingLlm] = useState(false);
  const [showWellDone, setShowWellDone] = useState(false); // State for "Well Done!" text

  // Audio for "Well Done!"
  const wellDoneAudio = useRef(null);
  useEffect(() => {
    wellDoneAudio.current = new Audio('https://www.soundhelix.com/examples/mp3/SoundHelix-Song-1.mp3'); // Placeholder sound
    // You might want a more appropriate child-friendly sound effect here.
    // For demo purposes, using a public domain song.
  }, []);

  // Use custom hook for document head management
  useDocumentHead({
    title: `Color the ${painting.name} - Paint My Play`,
    description: painting.description,
    keywords: painting.tags.join(', '),
    ogTitle: `Color the ${painting.name} - Paint My Play`,
    ogDescription: painting.description,
    ogImage: `https://example.com/assets/paintings/${painting.fileName}`, // Placeholder URL
    ogUrl: `https://example.com/coloring-pages/${painting.id}`, // Placeholder URL
    jsonLdSchema: {
      "@context": "https://schema.org",
      "@type": "CreativeWork",
      "name": `Color the ${painting.name} - Paint My Play`,
      "image": `https://example.com/assets/paintings/${painting.fileName}`,
      "keywords": painting.tags.join(', ')
    }
  });

  useEffect(() => {
    trackPageView(painting.name + ' Coloring Page', `/coloring-pages/${painting.id}`);
  }, [painting]);

  // Effect to apply colors to SVG paths on render or state change
  useEffect(() => {
    const svgElement = svgRef.current;
    if (!svgElement) return;

    // First, reset all paths to their original fill (white, or whatever is in the SVG initially)
    const allPaths = svgElement.querySelectorAll('path, circle, rect, ellipse, polygon, polyline, line, g');
    allPaths.forEach(el => {
      // Preserve the original fill if it was explicitly set, otherwise default to white
      if (!el.getAttribute('data-original-fill')) {
        el.setAttribute('data-original-fill', el.getAttribute('fill') || 'white');
      }
      el.setAttribute('fill', el.getAttribute('data-original-fill')); // Reset to original
    });

    // Then, apply the colors from coloredPaths
    for (const id in coloredPaths) {
      const element = svgElement.querySelector(`#${id}`);
      if (element) {
        element.setAttribute('fill', coloredPaths[id]);
      }
    }
  }, [coloredPaths, painting.id]); // Re-run when coloredPaths or painting changes

  const handleSvgClick = useCallback((event) => {
    const target = event.target;
    // We only want to color 'path', 'circle', 'rect', etc., elements, not the SVG itself or text
    const colorableTags = ['path', 'circle', 'rect', 'ellipse', 'polygon', 'polyline', 'line', 'g'];
    if (colorableTags.includes(target.tagName.toLowerCase())) {
      let elementToColor = target;

      // If the element doesn't have an ID, we might not be able to store its state effectively.
      // For coloring pages, it's best if designers provide IDs to all colorable regions.
      if (!elementToColor.id) {
          console.warn("Clicked SVG element has no ID. Cannot save its color state.", elementToColor);
          return; // Don't color if no ID to track
      }

      applyColor(elementToColor.id, selectedColor);

      // Play "Well Done!" sound and show text feedback
      if (wellDoneAudio.current) {
        wellDoneAudio.current.play().catch(e => console.error("Audio play failed:", e));
      }
      setShowWellDone(true);
      setTimeout(() => setShowWellDone(false), 1500); // Hide after 1.5 seconds
    }
  }, [selectedColor, applyColor]);

  const handleDownloadPng = useCallback(() => {
    const svgElement = svgRef.current;
    if (!svgElement) return;

    // Create a new SVG element with only the current colored state for download
    const tempSvg = document.createElementNS('http://www.w3.org/2000/svg', 'svg');
    tempSvg.setAttribute('width', svgElement.getAttribute('width') || '800'); // Use original SVG dimensions or default
    tempSvg.setAttribute('height', svgElement.getAttribute('height') || '800');
    tempSvg.setAttribute('viewBox', svgElement.getAttribute('viewBox') || '0 0 800 800');

    // Add necessary styles for default fills, strokes, etc., to the temporary SVG
    // This is a basic approach; more complex SVGs might require deep cloning with styles.
    // For simple line-art, direct copying of elements and their attributes should work for colors.
    const clonedSvgContent = svgElement.cloneNode(true);
    // Apply the current colored paths to the cloned SVG
    for (const id in coloredPaths) {
      const element = clonedSvgContent.querySelector(`#${id}`);
      if (element) {
        element.setAttribute('fill', coloredPaths[id]);
      }
    }
    // Remove original fill attributes that might interfere with rendering the cloned content.
    // Ensure all original styles are preserved if they are not fill-related.
    clonedSvgContent.querySelectorAll('*').forEach(el => {
      if (el.hasAttribute('data-original-fill')) {
        el.removeAttribute('data-original-fill');
      }
    });

    tempSvg.innerHTML = clonedSvgContent.innerHTML;

    // Convert SVG string to data URL
    const svgData = new XMLSerializer().serializeToString(tempSvg);
    const svgBlob = new Blob([svgData], { type: 'image/svg+xml;charset=utf-8' });
    const url = URL.createObjectURL(svgBlob);

    const canvas = document.createElement('canvas');
    const ctx = canvas.getContext('2d');

    // Set canvas dimensions based on SVG (important for high quality)
    const svgWidth = parseInt(tempSvg.getAttribute('width'));
    const svgHeight = parseInt(tempSvg.getAttribute('height'));
    canvas.width = svgWidth;
    canvas.height = svgHeight;


    // Check if canvg library is loaded. If not, inform the user explicitly.
    // This script should be added to your public/index.html file's <head> section:
    // <script src="https://cdn.jsdelivr.net/npm/canvg/dist/browser/canvg.min.js"></script>
    if (typeof window.canvg === 'undefined') {
        console.error("Canvg library not loaded. Please ensure <script src='https://cdn.jsdelivr.net/npm/canvg/dist/browser/canvg.min.js'></script> is in your index.html.");
        // Fallback for demo if canvg is not loaded:
        const img = new Image();
        img.onload = () => {
            canvas.width = img.width;
            canvas.height = img.height;
            ctx.drawImage(img, 0, 0);
            const pngUrl = canvas.toDataURL('image/png');
            const link = document.createElement('a');
            link.href = pngUrl;
            link.download = `${painting.name}_colored.png`;
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
            URL.revokeObjectURL(url);
        };
        img.onerror = (e) => {
            console.error("Error loading SVG for image conversion:", e);
            URL.revokeObjectURL(url);
        };
        img.src = url;
        return;
    }

    // Use canvg to render SVG to canvas
    window.canvg.Canvg.from(ctx, url, {
      ignoreMouse: true,
      ignoreAnimation: true,
      ignoreDimensions: true, // Use canvas dimensions
      scaleWidth: canvas.width,
      scaleHeight: canvas.height,
    }).then((v) => {
      v.render().then(() => {
        const pngUrl = canvas.toDataURL('image/png');
        const link = document.createElement('a');
        link.href = pngUrl;
        link.download = `${painting.name}_colored.png`;
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);
        URL.revokeObjectURL(url);
      });
    }).catch(error => {
      console.error("Error converting SVG to PNG with Canvg:", error);
      // Fallback for canvg error (simple image conversion)
      const img = new Image();
      img.onload = () => {
          canvas.width = img.width;
          canvas.height = img.height;
          ctx.drawImage(img, 0, 0);
          const pngUrl = canvas.toDataURL('image/png');
          const link = document.createElement('a');
          link.href = pngUrl;
          link.download = `${painting.name}_colored.png`;
          document.body.appendChild(link);
          link.click();
          document.body.removeChild(link);
          URL.revokeObjectURL(url);
      };
      img.onerror = (e) => {
          console.error("Error loading SVG for image conversion (fallback):", e);
          URL.revokeObjectURL(url);
      };
      img.src = url;
    });
  }, [painting, coloredPaths]);

  const handleGenerateStory = async () => {
    setIsLoadingLlm(true);
    const prompt = `Write a very short, simple, and child-friendly story (2-3 sentences) about a ${painting.name} from the category ${painting.category}. The story should be positive and fun.`;
    const story = await generateLlmContent(prompt);
    setStoryContent(story);
    setShowLlmOutputModal(true);
    setIsLoadingLlm(false);
  };

  const handleGenerateFunFact = async () => {
    setIsLoadingLlm(true);
    const prompt = `Give me one super fun and interesting fact for kids about a ${painting.name} from the category ${painting.category}. Keep it to a single sentence.`;
    const fact = await generateLlmContent(prompt);
    setFunFactContent(fact);
    // Display temporarily
    setTimeout(() => setFunFactContent(''), 7000); // Clear after 7 seconds
    setIsLoadingLlm(false);
  };

  return (
    <div className="flex flex-col items-center p-4 min-h-screen bg-gradient-to-br from-purple-100 to-pink-100 font-permanent-marker text-gray-800">
      {/* Navigation and Controls */}
      <div className="w-full max-w-4xl flex flex-wrap justify-center md:justify-between items-center bg-white p-3 md:p-4 rounded-xl shadow-lg mb-4 gap-2">
        <button
          onClick={onBackToGallery}
          className="bg-blue-500 hover:bg-blue-600 text-white font-bold py-3 px-6 rounded-full shadow-lg transition transform hover:scale-105 active:scale-95 text-sm md:text-base flex items-center justify-center min-w-[120px]"
        >
          <svg xmlns="http://www.w3.org/2000/svg" className="h-5 w-5 mr-1" viewBox="0 0 20 20" fill="currentColor">
            <path fillRule="evenodd" d="M9.707 16.707a1 1 0 01-1.414 0l-6-6a1 1 0 010-1.414l6-6a1 1 0 011.414 1.414L5.414 9H16a1 1 0 110 2H5.414l4.293 4.293a1 1 0 010 1.414z" clipRule="evenodd" />
          </svg>
          Gallery
        </button>
        <h2 className="text-3xl md:text-4xl font-extrabold text-center text-purple-700 select-none">
          {painting.name}
        </h2>
        <div className="flex flex-wrap justify-center gap-2">
          <button
            onClick={undo}
            disabled={historyIndex === 0}
            className={`bg-yellow-500 hover:bg-yellow-600 text-white font-bold py-3 px-6 rounded-full shadow-lg transition transform hover:scale-105 active:scale-95 text-sm md:text-base flex items-center justify-center min-w-[100px] ${historyIndex === 0 ? 'opacity-50 cursor-not-allowed' : ''}`}
          >
            <span role="img" aria-label="undo" className="text-xl mr-1">↩️</span> Undo
          </button>
          <button
            onClick={reset}
            className="bg-red-500 hover:bg-red-600 text-white font-bold py-3 px-6 rounded-full shadow-lg transition transform hover:scale-105 active:scale-95 text-sm md:text-base flex items-center justify-center min-w-[100px]"
          >
            <span role="img" aria-label="reset" className="text-xl mr-1">🔄</span> Reset
          </button>
          <button
            onClick={handleDownloadPng}
            className="bg-green-500 hover:bg-green-600 text-white font-bold py-3 px-6 rounded-full shadow-lg transition transform hover:scale-105 active:scale-95 text-sm md:text-base flex items-center justify-center min-w-[120px]"
          >
            <span role="img" aria-label="download" className="text-xl mr-1">⬇️</span> Download
          </button>
        </div>
      </div>

      {/* Main Coloring Area */}
      <div className="relative w-full max-w-4xl aspect-square bg-white rounded-xl shadow-lg overflow-hidden border-4 border-purple-400">
        <div
          ref={svgRef}
          className="w-full h-full flex items-center justify-center p-4" // Padding inside the container
          dangerouslySetInnerHTML={{ __html: painting.svgContent }}
          onClick={handleSvgClick}
        ></div>
        {/* "Well Done!" feedback */}
        {showWellDone && (
          <div className="absolute inset-0 flex items-center justify-center z-10 pointer-events-none">
            <span className="text-6xl font-extrabold text-green-600 bg-white bg-opacity-80 px-8 py-4 rounded-full shadow-2xl animate-fade-in-out">
              🎉 Well Done! 🎉
            </span>
          </div>
        )}
      </div>

      {/* LLM Feature Buttons */}
      <div className="w-full max-w-4xl flex flex-wrap justify-center md:justify-around items-center bg-white p-3 md:p-4 rounded-xl shadow-lg mt-4 gap-2">
        <button
          onClick={handleGenerateStory}
          disabled={isLoadingLlm}
          className={`bg-indigo-500 hover:bg-indigo-600 text-white font-bold py-3 px-6 rounded-full shadow-lg transition transform hover:scale-105 active:scale-95 text-base md:text-lg flex items-center justify-center min-w-[160px] ${isLoadingLlm ? 'opacity-50 cursor-not-allowed' : ''}`}
        >
          {isLoadingLlm ? (
            <svg className="animate-spin -ml-1 mr-3 h-5 w-5 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
              <circle className="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" strokeWidth="4"></circle>
              <path className="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
            </svg>
          ) : (
            <span role="img" aria-label="story" className="text-xl mr-2">📚</span>
          )}
          Tell Me a Story!
        </button>
        <button
          onClick={handleGenerateFunFact}
          disabled={isLoadingLlm}
          className={`bg-orange-500 hover:bg-orange-600 text-white font-bold py-3 px-6 rounded-full shadow-lg transition transform hover:scale-105 active:scale-95 text-base md:text-lg flex items-center justify-center min-w-[160px] ${isLoadingLlm ? 'opacity-50 cursor-not-allowed' : ''}`}
        >
          {isLoadingLlm ? (
            <svg className="animate-spin -ml-1 mr-3 h-5 w-5 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
              <circle className="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" strokeWidth="4"></circle>
              <path className="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
            </svg>
          ) : (
            <span role="img" aria-label="fact" className="text-xl mr-2">💡</span>
          )}
          Fun Fact!
        </button>
      </div>

      {/* Fun Fact Toast/Message */}
      {funFactContent && (
        <div className="fixed bottom-40 left-1/2 -translate-x-1/2 bg-blue-600 text-white p-4 rounded-full shadow-xl transition-all duration-500 z-50 animate-bounce-in-out font-permanent-marker text-center">
          <p className="text-lg font-semibold">{funFactContent}</p>
        </div>
      )}

      {/* Story Modal */}
      {showLlmOutputModal && (
        <LlmOutputModal
          title={`A Story About the ${painting.name}!`}
          content={storyContent}
          onClose={() => setShowLlmOutputModal(false)}
        />
      )}
    </div>
  );
};

// ColorPicker Component
const ColorPicker = ({ selectedColor, onColorSelect }) => {
  const colors = [
    '#EF4444', // Red 500
    '#3B82F6', // Blue 500
    '#22C55E', // Green 500
    '#F59E0B', // Amber 500
    '#EC4899', // Pink 500
    '#A855F7', // Purple 500
    '#14B8A6', // Teal 500
    '#6B7280', // Gray 500
    '#000000', // Black
    '#FFFFFF', // White - Eraser
  ];

  return (
    <div className="fixed bottom-0 left-0 right-0 z-50 bg-white p-3 md:p-4 shadow-xl flex flex-wrap justify-center gap-2 md:gap-3 rounded-t-3xl border-t-2 border-purple-300">
      {colors.map((color) => (
        <button
          key={color}
          onClick={() => onColorSelect(color)}
          className={`w-14 h-14 md:w-16 md:h-16 rounded-full shadow-md border-4 ${selectedColor === color ? 'border-purple-600 ring-4 ring-purple-300' : 'border-gray-200'} transition-all duration-200 transform hover:scale-110 active:scale-90 flex items-center justify-center text-2xl md:text-3xl`}
          style={{ backgroundColor: color }}
          aria-label={color === '#FFFFFF' ? 'Eraser' : `Select color ${color}`}
        >
          {color === '#FFFFFF' && <span role="img" aria-label="eraser">✏️</span>} {/* Eraser icon for white */}
        </button>
      ))}
    </div>
  );
};

// Gallery Component
const Gallery = ({ paintings, onSelectPainting }) => {
  const [searchTerm, setSearchTerm] = useState('');
  const [filterCategory, setFilterCategory] = useState('All');

  // Use custom hook for document head management
  useDocumentHead({
    title: `Paint My Play - Kids' Online Coloring Website`,
    description: `A fun and engaging online coloring website for kids! Explore animals, fruits, vehicles, and more. Paint and download your masterpieces.`,
    keywords: `kids coloring, online coloring, free coloring pages, animals coloring, fruits coloring, vehicles coloring, Paint My Play`,
    ogTitle: `Paint My Play - Kids' Online Coloring Website`,
    ogDescription: `A fun and engaging online coloring website for kids! Explore animals, fruits, vehicles, and more. Paint and download your masterpieces.`,
    ogImage: `https://example.com/logo.png`, // Placeholder URL
    ogUrl: `https://example.com/`, // Placeholder URL
    jsonLdSchema: {
      "@context": "https://schema.org",
      "@type": "WebSite",
      "name": "Paint My Play",
      "url": "https://example.com/",
      "potentialAction": {
        "@type": "SearchAction",
        "target": "https://example.com/?q={search_term_string}",
        "query-input": "required name=search_term_string"
      }
    }
  });


  useEffect(() => {
    trackPageView('Coloring Page Gallery', '/');
  }, []);

  const categories = ['All', ...new Set(paintings.map((p) => p.category))];

  const filteredPaintings = paintings.filter((painting) => {
    const matchesSearch = painting.name.toLowerCase().includes(searchTerm.toLowerCase()) ||
                          painting.tags.some(tag => tag.toLowerCase().includes(searchTerm.toLowerCase()));
    const matchesCategory = filterCategory === 'All' || painting.category === filterCategory;
    return matchesSearch && matchesCategory;
  });

  return (
    <div className="flex flex-col items-center p-4 pt-16 md:pt-4 min-h-screen bg-gradient-to-br from-purple-100 to-pink-100 font-permanent-marker text-gray-800">
      {/* Header */}
      <header className="fixed top-0 left-0 right-0 z-40 w-full bg-white p-4 shadow-lg flex flex-col md:flex-row items-center justify-between rounded-b-xl">
        <div className="flex items-center mb-2 md:mb-0">
          <img src="https://placehold.co/50x50/FF69B4/FFFFFF?text=🎨PMP" alt="Paint My Play Logo" className="h-12 w-12 mr-3 rounded-full shadow-md" /> {/* Placeholder Logo */}
          <h1 className="text-4xl font-extrabold text-purple-700 select-none">Paint My Play</h1>
        </div>
        <div className="flex flex-col sm:flex-row gap-2 w-full md:w-auto">
          <input
            type="text"
            placeholder="Search by name or tag..."
            value={searchTerm}
            onChange={(e) => setSearchTerm(e.target.value)}
            className="p-3 border-2 border-gray-300 rounded-full focus:ring-4 focus:ring-blue-400 outline-none w-full sm:w-64 text-lg"
          />
          <select
            value={filterCategory}
            onChange={(e) => setFilterCategory(e.target.value)}
            className="p-3 border-2 border-gray-300 rounded-full focus:ring-4 focus:ring-blue-400 outline-none w-full sm:w-auto text-lg"
          >
            {categories.map((cat) => (
              <option key={cat} value={cat}>
                {cat}
              </option>
            ))}
          </select>
        </div>
      </header>
      <div className="w-full max-w-6xl mt-28 md:mt-4 p-4 grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">
        {filteredPaintings.length === 0 ? (
          <p className="col-span-full text-center text-2xl text-gray-600 p-8">No coloring pages found. Try a different search or filter! 😞</p>
        ) : (
          filteredPaintings.map((painting) => (
            <div
              key={painting.id}
              className="bg-white rounded-xl shadow-lg overflow-hidden cursor-pointer transform transition-all duration-300 hover:scale-105 hover:shadow-xl active:scale-95 border-4 border-purple-300 flex flex-col"
              onClick={() => onSelectPainting(painting)}
            >
              <div className="relative w-full h-48 bg-gray-50 flex items-center justify-center p-2">
                {/* Displaying a simplified SVG preview here. In a real scenario, you might pre-render PNG thumbnails. */}
                <div
                  className="w-full h-full max-w-full max-h-full"
                  dangerouslySetInnerHTML={{ __html: painting.svgContent }}
                  style={{ pointerEvents: 'none' }} // Prevent coloring on gallery preview
                />
              </div>
              <div className="p-4 flex-grow flex flex-col justify-between">
                <h3 className="text-xl font-bold text-center text-purple-600 mb-2">{painting.name}</h3>
                <div className="text-base text-gray-500 text-center">
                  <span className="bg-blue-100 text-blue-800 px-3 py-1 rounded-full text-sm font-semibold shadow-sm">{painting.category}</span>
                </div>
              </div>
            </div>
          ))
        )}
      </div>
    </div>
  );
};

// Main App Component
function App() {
  const [currentPage, setCurrentPage] = useState('gallery'); // 'gallery' or 'coloring'
  const [selectedPainting, setSelectedPainting] = useState(null);
  const [selectedColor, setSelectedColor] = useState('#EF4444'); // Default red

  // Load all paintings data (simulated from paintings.json)
  const paintings = demoPaintingsData;

  const handleSelectPainting = (painting) => {
    setSelectedPainting(painting);
    setCurrentPage('coloring');
  };

  const handleBackToGallery = () => {
    setCurrentPage('gallery');
    setSelectedPainting(null);
  };

  const handleNextPainting = useCallback(() => {
    if (selectedPainting) {
      const currentIndex = paintings.findIndex(p => p.id === selectedPainting.id);
      const nextIndex = (currentIndex + 1) % paintings.length;
      setSelectedPainting(paintings[nextIndex]);
    }
  }, [selectedPainting, paintings]);

  const handlePrevPainting = useCallback(() => {
    if (selectedPainting) {
      const currentIndex = paintings.findIndex(p => p.id === selectedPainting.id);
      const prevIndex = (currentIndex - 1 + paintings.length) % paintings.length;
      setSelectedPainting(paintings[prevIndex]);
    }
  }, [selectedPainting, paintings]);

  return (
    <div className="min-h-screen bg-gray-50 pb-20 font-permanent-marker"> {/* pb-20 to account for sticky color picker */}
      {/* Google AdSense Placeholder (actual integration requires AdSense code) */}
      <div className="ad-unit bg-yellow-50 p-2 text-center text-sm text-gray-600 border-b border-yellow-200">
        {/*
          <script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-XXXXXXXXXXXXX" crossorigin="anonymous"></script>
          <ins class="adsbygoogle"
               style="display:block"
               data-ad-client="ca-pub-XXXXXXXXXXXXX"
               data-ad-slot="XXXXXXXXXX"
               data-ad-format="auto"
               data-full-width-responsive="true"></ins>
          <script>(adsbygoogle = window.adsbygoogle || []).push({});</script>
        */}
        <p>Your Google AdSense Ad Here</p>
      </div>

      {currentPage === 'gallery' && (
        <Gallery paintings={paintings} onSelectPainting={handleSelectPainting} />
      )}

      {currentPage === 'coloring' && selectedPainting && (
        <>
          <ColoringPage
            painting={selectedPainting}
            selectedColor={selectedColor}
            onBackToGallery={handleBackToGallery}
          />
          <ColorPicker selectedColor={selectedColor} onColorSelect={setSelectedColor} />

          {/* Navigation Buttons for Coloring Page (Next/Prev) */}
          <div className="fixed bottom-24 left-0 right-0 z-40 flex justify-center gap-4 p-4 md:p-6">
            <button
              onClick={handlePrevPainting}
              className="bg-purple-500 hover:bg-purple-600 text-white font-bold py-3 px-6 rounded-full shadow-lg transition transform hover:scale-110 active:scale-90 text-lg flex items-center justify-center min-w-[120px]"
            >
              <span role="img" aria-label="previous" className="text-xl mr-2">◀️</span> Previous
            </button>
            <button
              onClick={handleNextPainting}
              className="bg-purple-500 hover:bg-purple-600 text-white font-bold py-3 px-6 rounded-full shadow-lg transition transform hover:scale-110 active:scale-90 text-lg flex items-center justify-center min-w-[120px]"
            >
              Next <span role="img" aria-label="next" className="text-xl ml-2">▶️</span>
            </button>
          </div>
        </>
      )}
    </div>
  );
}

export default App;


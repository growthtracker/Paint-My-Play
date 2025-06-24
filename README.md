# Paint-My-Play
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
        <rect x="50" y="50" width="300" height="300" rx="30" stroke="black" stroke-width="2" fill="white"/>
        <circle cx="200" cy="200" r="100" stroke="black" stroke-width="2" fill="white"/>
        <path d="M150 150 C175 120, 225 120, 250 150 C270 170, 270 230, 250 250 C225 280, 175 280, 150 250 C130 230, 130 170, 150 150Z" stroke="black" stroke-width="2" fill="white"/>
        <path id="lion-face" d="M190 190 L210 190 L200 210 Z" stroke="black" stroke-width="2" fill="white"/>
        <circle id="lion-eye-left" cx="170" cy="170" r="10" fill="black"/>
        <circle id="lion-eye-right" cx="230" cy="170" r="10" fill="black"/>
        <ellipse id="lion-nose" cx="200" cy="220" rx="15" ry="10" fill="black"/>
        <path id="lion-mane-outer" d="M100 100 A100 100 0 0 1 300 100 A100 100 0 0 1 300 300 A100 100 0 0 1 100 300 A100 100 0 0 1 100 100 Z" stroke="black" stroke-width="2" fill="none"/>
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
        <path d="M200 50 C150 50, 100 100, 100 175 C100 250, 150 350, 200 350 C250 350, 300 250, 300 175 C300 100, 250 50, 200 50Z" stroke="black" stroke-width="2" fill="white"/>
        <path d="M190 50 C180 30, 220 30, 210 50" stroke="black" stroke-width="2" fill="white"/>
        <circle cx="200" cy="350" r="10" fill="black"/>
        <path d="M220 60 Q230 40, 240 50" stroke="black" stroke-width="2" fill="white"/>
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
        <rect x="50" y="200" width="300" height="80" rx="10" stroke="black" stroke-width="2" fill="white"/>
        <path d="M80 200 L120 150 L280 150 L320 200 Z" stroke="black" stroke-width="2" fill="white"/>
        <circle cx="120" cy="280" r="30" stroke="black" stroke-width="2" fill="white"/>
        <circle cx="280" cy="280" r="30" stroke="black" stroke-width="2" fill="white"/>
        <rect x="130" y="160" width="140" height="30" rx="5" stroke="black" stroke-width="2" fill="white"/>
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
        <path d="M150 100 C100 150, 100 250, 150 300 C200 350, 300 300, 300 250 C300 200, 250 50, 200 100 C175 125, 150 100, 150 100Z" stroke="black" stroke-width="2" fill="white"/>
        <path d="M200 300 L200 350 C180 370, 220 370, 200 350 Z" stroke="black" stroke-width="2" fill="white"/>
        <circle cx="170" cy="150" r="20" stroke="black" stroke-width="2" fill="white"/>
        <circle cx="230" cy="150" r="20" stroke="black" stroke-width="2" fill="white"/>
        <circle cx="200" cy="110" r="20" stroke="black" stroke-width="2" fill="white"/>
        <circle cx="150" cy="200" r="20" stroke="black" stroke-width="2" fill="white"/>
        <circle cx="250" cy="200" r="20" stroke="black" stroke-width="2" fill="white"/>
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
        <rect x="100" y="200" width="200" height="150" stroke="black" stroke-width="2" fill="white"/>
        <path d="M100 200 L200 100 L300 200 Z" stroke="black" stroke-width="2" fill="white"/>
        <rect x="160" y="250" width="80" height="100" stroke="black" stroke-width="2" fill="white"/>
        <rect x="120" y="220" width="40" height="40" stroke="black" stroke-width="2" fill="white"/>
        <rect x="240" y="220" width="40" height="40" stroke="black" stroke-width="2" fill="white"/>
        <circle cx="200" cy="120" r="10" fill="black"/>
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

// ColoringPage Component
const ColoringPage = ({ painting, selectedColor, onBackToGallery }) => {
  const svgRef = useRef(null);
  const { coloredPaths, applyColor, undo, reset, historyIndex, historyLength } = useColoringState(painting.id, painting.svgContent);

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

      // If the target is a 'g' element, we might want to color all its children or the group itself.
      // For simplicity, let's target the immediate element clicked.
      // If the element doesn't have an ID, we might not be able to store its state effectively.
      // For coloring pages, it's best if designers provide IDs to all colorable regions.
      if (!elementToColor.id) {
          // If no ID, check if it's a child of a group with an ID, but for basic SVGs, let's use the element directly.
          // Fallback: If no ID, try to assign a temporary unique ID or ignore.
          // For a coloring page, ideally, all fillable parts have IDs.
          console.warn("Clicked SVG element has no ID. Cannot save its color state.", elementToColor);
          return; // Don't color if no ID to track
      }

      applyColor(elementToColor.id, selectedColor);
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

    // Load canvg library
    // For a real project, consider installing via npm and importing.
    // For this immersive, we assume it's loaded via CDN in index.html (or just before this script).
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


  return (
    <div className="flex flex-col items-center p-4 min-h-screen bg-gradient-to-br from-purple-100 to-pink-100 font-inter text-gray-800">
      {/* Navigation and Controls */}
      <div className="w-full max-w-4xl flex flex-wrap justify-center md:justify-between items-center bg-white p-3 md:p-4 rounded-xl shadow-lg mb-4 gap-2">
        <button
          onClick={onBackToGallery}
          className="bg-blue-500 hover:bg-blue-600 text-white font-bold py-2 px-4 rounded-full shadow-md transition transform hover:scale-105 active:scale-95 text-sm md:text-base flex items-center"
        >
          <svg xmlns="http://www.w3.org/2000/svg" className="h-5 w-5 mr-1" viewBox="0 0 20 20" fill="currentColor">
            <path fillRule="evenodd" d="M9.707 16.707a1 1 0 01-1.414 0l-6-6a1 1 0 010-1.414l6-6a1 1 0 011.414 1.414L5.414 9H16a1 1 0 110 2H5.414l4.293 4.293a1 1 0 010 1.414z" clipRule="evenodd" />
          </svg>
          Back to Gallery
        </button>
        <h2 className="text-xl md:text-2xl font-extrabold text-center text-purple-600 select-none">
          {painting.name}
        </h2>
        <div className="flex flex-wrap justify-center gap-2">
          <button
            onClick={undo}
            disabled={historyIndex === 0}
            className={`bg-yellow-500 hover:bg-yellow-600 text-white font-bold py-2 px-4 rounded-full shadow-md transition transform hover:scale-105 active:scale-95 text-sm md:text-base flex items-center ${historyIndex === 0 ? 'opacity-50 cursor-not-allowed' : ''}`}
          >
            <svg xmlns="http://www.w3.org/2000/svg" className="h-5 w-5 mr-1" viewBox="0 0 20 20" fill="currentColor">
              <path fillRule="evenodd" d="M12.707 5.293a1 1 0 010 1.414L9.414 10l3.293 3.293a1 1 0 01-1.414 1.414l-4-4a1 1 0 010-1.414l4-4a1 1 0 011.414 0z" clipRule="evenodd" />
            </svg>
            Undo
          </button>
          <button
            onClick={reset}
            className="bg-red-500 hover:bg-red-600 text-white font-bold py-2 px-4 rounded-full shadow-md transition transform hover:scale-105 active:scale-95 text-sm md:text-base flex items-center"
          >
            <svg xmlns="http://www.w3.org/2000/svg" className="h-5 w-5 mr-1" viewBox="0 0 20 20" fill="currentColor">
              <path fillRule="evenodd" d="M11.493 14.907a1 1 0 01-1.486 1.39l-6-6a1 1 0 010-1.39l6-6a1 1 0 011.486 1.39L6.914 10l4.579 4.907zM17.486 10l-6-6a1 1 0 010-1.39l-6 6a1 1 0 011.486 1.39l4.579-4.907 4.579 4.907a1 1 0 010-1.39z" clipRule="evenodd" />
            </svg>
            Reset
          </button>
          <button
            onClick={handleDownloadPng}
            className="bg-green-500 hover:bg-green-600 text-white font-bold py-2 px-4 rounded-full shadow-md transition transform hover:scale-105 active:scale-95 text-sm md:text-base flex items-center"
          >
            <svg xmlns="http://www.w3.org/2000/svg" className="h-5 w-5 mr-1" viewBox="0 0 20 20" fill="currentColor">
              <path fillRule="evenodd" d="M3 17a1 1 0 011-1h12a1 1 0 110 2H4a1 1 0 01-1-1zm3.293-7.707a1 1 0 011.414 0L10 11.586l2.293-2.293a1 1 0 111.414 1.414l-3 3a1 1 0 01-1.414 0l-3-3a1 1 0 010-1.414z" clipRule="evenodd" />
              <path fillRule="evenodd" d="M10 3a1 1 0 011 1v7a1 1 0 11-2 0V4a1 1 0 011-1z" clipRule="evenodd" />
            </svg>
            Download PNG
          </button>
        </div>
      </div>

      {/* Main Coloring Area */}
      <div className="relative w-full max-w-4xl aspect-square bg-white rounded-xl shadow-lg overflow-hidden border-2 border-purple-300">
        <div
          ref={svgRef}
          className="w-full h-full flex items-center justify-center p-4" // Padding inside the container
          dangerouslySetInnerHTML={{ __html: painting.svgContent }}
          onClick={handleSvgClick}
        ></div>
      </div>
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
    '#FFFFFF', // White
  ];

  return (
    <div className="fixed bottom-0 left-0 right-0 z-50 bg-white p-3 md:p-4 shadow-xl flex flex-wrap justify-center gap-2 md:gap-3 rounded-t-3xl border-t-2 border-purple-300">
      {colors.map((color) => (
        <button
          key={color}
          onClick={() => onColorSelect(color)}
          className={`w-10 h-10 md:w-12 md:h-12 rounded-full shadow-md border-2 ${selectedColor === color ? 'border-purple-600 ring-4 ring-purple-300' : 'border-gray-200'} transition-all duration-200 transform hover:scale-110 active:scale-90`}
          style={{ backgroundColor: color }}
          aria-label={`Select color ${color}`}
        />
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
    <div className="flex flex-col items-center p-4 pt-16 md:pt-4 min-h-screen bg-gradient-to-br from-purple-100 to-pink-100 font-inter text-gray-800">
      {/* Header */}
      <header className="fixed top-0 left-0 right-0 z-40 w-full bg-white p-4 shadow-lg flex flex-col md:flex-row items-center justify-between rounded-b-xl">
        <div className="flex items-center mb-2 md:mb-0">
          <img src="https://placehold.co/40x40/FF69B4/FFFFFF?text=PMP" alt="Paint My Play Logo" className="h-10 w-10 mr-3 rounded-full" /> {/* Placeholder Logo */}
          <h1 className="text-3xl font-extrabold text-purple-700 select-none">Paint My Play</h1>
        </div>
        <div className="flex flex-col sm:flex-row gap-2 w-full md:w-auto">
          <input
            type="text"
            placeholder="Search by name or tag..."
            value={searchTerm}
            onChange={(e) => setSearchTerm(e.target.value)}
            className="p-2 border border-gray-300 rounded-full focus:ring-2 focus:ring-blue-400 outline-none w-full sm:w-64"
          />
          <select
            value={filterCategory}
            onChange={(e) => setFilterCategory(e.target.value)}
            className="p-2 border border-gray-300 rounded-full focus:ring-2 focus:ring-blue-400 outline-none w-full sm:w-auto"
          >
            {categories.map((cat) => (
              <option key={cat} value={cat}>
                {cat}
              </option>
            ))}
          </select>
        </div>
      </header>
      <div className="w-full max-w-6xl mt-24 md:mt-4 p-4 grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">
        {filteredPaintings.length === 0 ? (
          <p className="col-span-full text-center text-xl text-gray-600 p-8">No coloring pages found. Try a different search or filter!</p>
        ) : (
          filteredPaintings.map((painting) => (
            <div
              key={painting.id}
              className="bg-white rounded-xl shadow-lg overflow-hidden cursor-pointer transform transition-all duration-300 hover:scale-105 hover:shadow-xl active:scale-95 border-2 border-purple-200 flex flex-col"
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
                <h3 className="text-lg font-bold text-center text-purple-600 mb-2">{painting.name}</h3>
                <div className="text-sm text-gray-500 text-center">
                  <span className="bg-blue-100 text-blue-800 px-2 py-1 rounded-full text-xs font-semibold">{painting.category}</span>
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
    <div className="min-h-screen bg-gray-50 pb-20 font-inter"> {/* pb-20 to account for sticky color picker */}
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
              className="bg-purple-500 hover:bg-purple-600 text-white font-bold py-3 px-6 rounded-full shadow-lg transition transform hover:scale-110 active:scale-90 text-lg flex items-center"
            >
              <svg xmlns="http://www.w3.org/2000/svg" className="h-6 w-6 mr-2" fill="none" viewBox="0 0 24 24" stroke="currentColor" strokeWidth="2">
                <path strokeLinecap="round" strokeLinejoin="round" d="M11 19l-7-7 7-7m8 14l-7-7 7-7" />
              </svg>
              Previous
            </button>
            <button
              onClick={handleNextPainting}
              className="bg-purple-500 hover:bg-purple-600 text-white font-bold py-3 px-6 rounded-full shadow-lg transition transform hover:scale-110 active:scale-90 text-lg flex items-center"
            >
              Next
              <svg xmlns="http://www.w3.org/2000/svg" className="h-6 w-6 ml-2" fill="none" viewBox="0 0 24 24" stroke="currentColor" strokeWidth="2">
                <path strokeLinecap="round" strokeLinejoin="round" d="M13 5l7 7-7 7M6 5l7 7-7 7" />
              </svg>
            </button>
          </div>
        </>
      )}
    </div>
  );
}

export default App;


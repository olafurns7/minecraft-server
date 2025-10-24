Examples
End-to-end Apps SDK examples.

Overview
The Pizzaz demo app bundles a handful of UI components so you can see the full tool surface area end-to-end. The following sections walk through the MCP server and the component implementations that power those tools. You can find the “Pizzaz” demo app and other examples in our examples repository on GitHub.

Use these examples as blueprints when you assemble your own app.

MCP Source
This TypeScript server shows how to register multiple tools that share data with pre-built UI resources. Each resource call returns a Skybridge HTML shell, and every tool responds with matching metadata so ChatGPT knows which component to render.

import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { z } from "zod";

declare const server: McpServer;

// UI resource (no inline data assignment; host will inject data)
server.registerResource(
  "pizza-map",
  "ui://widget/pizza-map.html",
  {},
  async () => ({
    contents: [
      {
        uri: "ui://widget/pizza-map.html",
        mimeType: "text/html+skybridge",
        text: `
<div id="pizzaz-root"></div>
<link rel="stylesheet" href="https://persistent.oaistatic.com/ecosystem-built-assets/pizzaz-0038.css">
<script type="module" src="https://persistent.oaistatic.com/ecosystem-built-assets/pizzaz-0038.js"></script>
        `.trim(),
      },
    ],
  })
);

server.registerTool(
  "pizza-map",
  {
    title: "Show Pizza Map",
    _meta: {
      "openai/outputTemplate": "ui://widget/pizza-map.html",
      "openai/toolInvocation/invoking": "Hand-tossing a map",
      "openai/toolInvocation/invoked": "Served a fresh map",
    },
    inputSchema: { pizzaTopping: z.string() },
  },
  async () => {
    return {
      content: [{ type: "text", text: "Rendered a pizza map!" }],
      structuredContent: {},
    };
  }
);

server.registerResource(
  "pizza-carousel",
  "ui://widget/pizza-carousel.html",
  {},
  async () => ({
    contents: [
      {
        uri: "ui://widget/pizzaz-carousel.html",
        mimeType: "text/html+skybridge",
        text: `
<div id="pizzaz-carousel-root"></div>
<link rel="stylesheet" href="https://persistent.oaistatic.com/ecosystem-built-assets/pizzaz-carousel-0038.css">
<script type="module" src="https://persistent.oaistatic.com/ecosystem-built-assets/pizzaz-carousel-0038.js"></script>
        `.trim(),
      },
    ],
  })
);

server.registerTool(
  "pizza-carousel",
  {
    title: "Show Pizza Carousel",
    _meta: {
      "openai/outputTemplate": "ui://widget/pizza-carousel.html",
      "openai/toolInvocation/invoking": "Carousel some spots",
      "openai/toolInvocation/invoked": "Served a fresh carousel",
    },
    inputSchema: { pizzaTopping: z.string() },
  },
  async () => {
    return {
      content: [{ type: "text", text: "Rendered a pizza carousel!" }],
      structuredContent: {},
    };
  }
);

server.registerResource(
  "pizza-albums",
  "ui://widget/pizza-albums.html",
  {},
  async () => ({
    contents: [
      {
        uri: "ui://widget/pizzaz-albums.html",
        mimeType: "text/html+skybridge",
        text: `
<div id="pizzaz-albums-root"></div>
<link rel="stylesheet" href="https://persistent.oaistatic.com/ecosystem-built-assets/pizzaz-albums-0038.css">
<script type="module" src="https://persistent.oaistatic.com/ecosystem-built-assets/pizzaz-albums-0038.js"></script>
        `.trim(),
      },
    ],
  })
);

server.registerTool(
  "pizza-albums",
  {
    title: "Show Pizza Album",
    _meta: {
      "openai/outputTemplate": "ui://widget/pizza-albums.html",
      "openai/toolInvocation/invoking": "Hand-tossing an album",
      "openai/toolInvocation/invoked": "Served a fresh album",
    },
    inputSchema: { pizzaTopping: z.string() },
  },
  async () => {
    return {
      content: [{ type: "text", text: "Rendered a pizza album!" }],
      structuredContent: {},
    };
  }
);

server.registerResource(
  "pizza-list",
  "ui://widget/pizza-list.html",
  {},
  async () => ({
    contents: [
      {
        uri: "ui://widget/pizzaz-list.html",
        mimeType: "text/html+skybridge",
        text: `
<div id="pizzaz-list-root"></div>
<link rel="stylesheet" href="https://persistent.oaistatic.com/ecosystem-built-assets/pizzaz-list-0038.css">
<script type="module" src="https://persistent.oaistatic.com/ecosystem-built-assets/pizzaz-list-0038.js"></script>
        `.trim(),
      },
    ],
  })
);

server.registerTool(
  "pizza-list",
  {
    title: "Show Pizza List",
    _meta: {
      "openai/outputTemplate": "ui://widget/pizza-list.html",
      "openai/toolInvocation/invoking": "Hand-tossing a list",
      "openai/toolInvocation/invoked": "Served a fresh list",
    },
    inputSchema: { pizzaTopping: z.string() },
  },
  async () => {
    return {
      content: [{ type: "text", text: "Rendered a pizza list!" }],
      structuredContent: {},
    };
  }
);

server.registerResource(
  "pizza-video",
  "ui://widget/pizza-video.html",
  {},
  async () => ({
    contents: [
      {
        uri: "ui://widget/pizzaz-video.html",
        mimeType: "text/html+skybridge",
        text: `
<div id="pizzaz-video-root"></div>
<link rel="stylesheet" href="https://persistent.oaistatic.com/ecosystem-built-assets/pizzaz-video-0038.css">
<script type="module" src="https://persistent.oaistatic.com/ecosystem-built-assets/pizzaz-video-0038.js"></script>
        `.trim(),
      },
    ],
  })
);

server.registerTool(
  "pizza-video",
  {
    title: "Show Pizza Video",
    _meta: {
      "openai/outputTemplate": "ui://widget/pizza-video.html",
      "openai/toolInvocation/invoking": "Hand-tossing a video",
      "openai/toolInvocation/invoked": "Served a fresh video",
    },
    inputSchema: { pizzaTopping: z.string() },
  },
  async () => {
    return {
      content: [{ type: "text", text: "Rendered a pizza video!" }],
      structuredContent: {},
    };
  }
);
Pizzaz Map Source
Screenshot of the Pizzaz map component

The map component is a React + Mapbox client that syncs its state back to ChatGPT. It renders marker interactions, inspector routing, and fullscreen handling so you can study a heavier, stateful component example.

import React, { useEffect, useRef, useState } from "react";
import mapboxgl from "mapbox-gl";
import "mapbox-gl/dist/mapbox-gl.css";
import { createRoot } from "react-dom/client";
import markers from "./markers.json";
import { AnimatePresence } from "framer-motion";
import Inspector from "./Inspector";
import Sidebar from "./Sidebar";
import { useOpenaiGlobal } from "../use-openai-global";
import { useMaxHeight } from "../use-max-height";
import { Maximize2 } from "lucide-react";
import {
  useNavigate,
  useLocation,
  Routes,
  Route,
  BrowserRouter,
  Outlet,
} from "react-router-dom";

mapboxgl.accessToken =
  "pk.eyJ1IjoiZXJpY25pbmciLCJhIjoiY21icXlubWM1MDRiczJvb2xwM2p0amNyayJ9.n-3O6JI5nOp_Lw96ZO5vJQ";

function fitMapToMarkers(map, coords) {
  if (!map || !coords.length) return;
  if (coords.length === 1) {
    map.flyTo({ center: coords[0], zoom: 12 });
    return;
  }
  const bounds = coords.reduce(
    (b, c) => b.extend(c),
    new mapboxgl.LngLatBounds(coords[0], coords[0])
  );
  map.fitBounds(bounds, { padding: 60, animate: true });
}

export default function App() {
  const mapRef = useRef(null);
  const mapObj = useRef(null);
  const markerObjs = useRef([]);
  const places = markers?.places || [];
  const markerCoords = places.map((p) => p.coords);
  const navigate = useNavigate();
  const location = useLocation();
  const selectedId = React.useMemo(() => {
    const match = location?.pathname?.match(/(?:^|\/)place\/([^/]+)/);
    return match && match[1] ? match[1] : null;
  }, [location?.pathname]);
  const selectedPlace = places.find((p) => p.id === selectedId) || null;
  const [viewState, setViewState] = useState(() => ({
    center: markerCoords.length > 0 ? markerCoords[0] : [0, 0],
    zoom: markerCoords.length > 0 ? 12 : 2,
  }));
  const displayMode = useOpenaiGlobal("displayMode");
  const allowInspector = displayMode === "fullscreen";
  const maxHeight = useMaxHeight() ?? undefined;

  useEffect(() => {
    if (mapObj.current) return;
    mapObj.current = new mapboxgl.Map({
      container: mapRef.current,
      style: "mapbox://styles/mapbox/streets-v12",
      center: markerCoords.length > 0 ? markerCoords[0] : [0, 0],
      zoom: markerCoords.length > 0 ? 12 : 2,
      attributionControl: false,
    });
    addAllMarkers(places);
    setTimeout(() => {
      fitMapToMarkers(mapObj.current, markerCoords);
    }, 0);
    // after first paint
    requestAnimationFrame(() => mapObj.current.resize());

    // or keep it in sync with window resizes
    window.addEventListener("resize", mapObj.current.resize);

    return () => {
      window.removeEventListener("resize", mapObj.current.resize);
      mapObj.current.remove();
    };
    // eslint-disable-next-line
  }, []);

  useEffect(() => {
    if (!mapObj.current) return;
    const handler = () => {
      const c = mapObj.current.getCenter();
      setViewState({ center: [c.lng, c.lat], zoom: mapObj.current.getZoom() });
    };
    mapObj.current.on("moveend", handler);
    return () => {
      mapObj.current.off("moveend", handler);
    };
  }, []);

  function addAllMarkers(placesList) {
    markerObjs.current.forEach((m) => m.remove());
    markerObjs.current = [];
    placesList.forEach((place) => {
      const marker = new mapboxgl.Marker({
        color: "#F46C21",
      })
        .setLngLat(place.coords)
        .addTo(mapObj.current);
      const el = marker.getElement();
      if (el) {
        el.style.cursor = "pointer";
        el.addEventListener("click", () => {
          navigate(`place/${place.id}`);
          panTo(place.coords, { offsetForInspector: true });
        });
      }
      markerObjs.current.push(marker);
    });
  }

  function getInspectorHalfWidthPx() {
    if (displayMode !== "fullscreen") return 0;
    if (typeof window === "undefined") return 0;
    const isLgUp =
      window.matchMedia && window.matchMedia("(min-width: 1024px)").matches;
    if (!isLgUp) return 0;
    const el = document.querySelector(".pizzaz-inspector");
    const w = el ? el.getBoundingClientRect().width : 360;
    return Math.round(w / 2);
  }

  function panTo(
    coord,
    { offsetForInspector } = { offsetForInspector: false }
  ) {
    if (!mapObj.current) return;
    const halfInspector = offsetForInspector ? getInspectorHalfWidthPx() : 0;
    const flyOpts = {
      center: coord,
      zoom: 14,
      speed: 1.2,
      curve: 1.6,
    };
    if (halfInspector) {
      flyOpts.offset = [-halfInspector, 0];
    }
    mapObj.current.flyTo(flyOpts);
  }

  useEffect(() => {
    if (!mapObj.current) return;
    addAllMarkers(places);
  }, [places]);

  // Pan the map when the selected place changes via routing
  useEffect(() => {
    if (!mapObj.current || !selectedPlace) return;
    panTo(selectedPlace.coords, { offsetForInspector: true });
  }, [selectedId]);

  // Ensure Mapbox resizes when container maxHeight/display mode changes
  useEffect(() => {
    if (!mapObj.current) return;
    mapObj.current.resize();
  }, [maxHeight, displayMode]);

  useEffect(() => {
    if (
      typeof window !== "undefined" &&
      window.oai &&
      typeof window.oai.widget.setState === "function"
    ) {
      window.oai.widget.setState({
        center: viewState.center,
        zoom: viewState.zoom,
        markers: markerCoords,
      });
    }
  }, [viewState, markerCoords]);

  return (
    <div
      style={{
        maxHeight,
        height: displayMode === "fullscreen" ? maxHeight : 480,
      }}
      className={
        "relative antialiased w-full min-h-[480px] overflow-hidden " +
        (displayMode === "fullscreen"
          ? "rounded-none border-0"
          : "border border-black/10 dark:border-white/10 rounded-2xl sm:rounded-3xl")
      }
    >
      <Outlet />
      {displayMode !== "fullscreen" && (
        <button
          aria-label="Enter fullscreen"
          className="absolute top-4 right-4 z-30 rounded-full bg-white text-black shadow-lg ring ring-black/5 p-2.5 pointer-events-auto"
          onClick={() => {
            if (selectedId) {
              navigate("..", { replace: true });
            }
            if (window?.openai?.requestDisplayMode) {
              window.openai.requestDisplayMode({ mode: "fullscreen" });
            }
          }}
        >
          <Maximize2
            strokeWidth={1.5}
            className="h-4.5 w-4.5"
            aria-hidden="true"
          />
        </button>
      )}
      {/* Sidebar */}
      <Sidebar
        places={places}
        selectedId={selectedId}
        onSelect={(place) => {
          navigate(`place/${place.id}`);
          panTo(place.coords, { offsetForInspector: true });
        }}
      />

      {/* Inspector (right) */}
      <AnimatePresence>
        {allowInspector && selectedPlace && (
          <Inspector
            key={selectedPlace.id}
            place={selectedPlace}
            onClose={() => navigate("..")}
          />
        )}
      </AnimatePresence>

      {/* Map */}
      <div
        className={
          "absolute inset-0 overflow-hidden" +
          (displayMode === "fullscreen"
            ? " md:left-[340px] md:right-4 md:top-4 md:bottom-4 border border-black/10 md:rounded-3xl"
            : "")
        }
      >
        <div
          ref={mapRef}
          className="w-full h-full relative absolute bottom-0 left-0 right-0"
          style={{
            maxHeight,
            height: displayMode === "fullscreen" ? maxHeight : undefined,
          }}
        />
      </div>
    </div>
  );
}

function RouterRoot() {
  return (
    <Routes>
      <Route path="*" element={<App />}>
        <Route path="place/:placeId" element={<></>} />
      </Route>
    </Routes>
  );
}

createRoot(document.getElementById("pizzaz-root")).render(
  <BrowserRouter>
    <RouterRoot />
  </BrowserRouter>
);
Pizzaz Carousel Source
Screenshot of the Pizzaz carousel component

This carousel demonstrates how to build a lightweight gallery view. It leans on embla-carousel for touch-friendly scrolling and wires up button state so the component stays reactive without any server roundtrips.

import useEmblaCarousel from "embla-carousel-react";
import { ArrowLeft, ArrowRight } from "lucide-react";
import React from "react";
import { Star } from "lucide-react";
import { createRoot } from "react-dom/client";
import markers from "../pizzaz/markers.json";
import PlaceCard from "./PlaceCard";

function App() {
  const places = markers?.places || [];
  const [emblaRef, emblaApi] = useEmblaCarousel({
    align: "center",
    loop: false,
    containScroll: "trimSnaps",
    slidesToScroll: "auto",
    dragFree: false,
  });
  const [canPrev, setCanPrev] = React.useState(false);
  const [canNext, setCanNext] = React.useState(false);

  React.useEffect(() => {
    if (!emblaApi) return;
    const updateButtons = () => {
      setCanPrev(emblaApi.canScrollPrev());
      setCanNext(emblaApi.canScrollNext());
    };
    updateButtons();
    emblaApi.on("select", updateButtons);
    emblaApi.on("reInit", updateButtons);
    return () => {
      emblaApi.off("select", updateButtons);
      emblaApi.off("reInit", updateButtons);
    };
  }, [emblaApi]);

  return (
    <div className="antialiased relative w-full text-black py-5">
      <div className="overflow-hidden" ref={emblaRef}>
        <div className="flex gap-4 items-stretch">
          {places.map((place) => (
            <PlaceCard key={place.id} place={place} />
          ))}
        </div>
      </div>
      {/* Edge gradients */}
      <div
        aria-hidden
        className={
          "pointer-events-none absolute inset-y-0 left-0 w-3 z-[5] transition-opacity duration-200 " +
          (canPrev ? "opacity-100" : "opacity-0")
        }
      >
        <div
          className="h-full w-full border-l border-black/15 bg-gradient-to-r from-black/10 to-transparent"
          style={{
            WebkitMaskImage:
              "linear-gradient(to bottom, transparent 0%, white 30%, white 70%, transparent 100%)",
            maskImage:
              "linear-gradient(to bottom, transparent 0%, white 30%, white 70%, transparent 100%)",
          }}
        />
      </div>
      <div
        aria-hidden
        className={
          "pointer-events-none absolute inset-y-0 right-0 w-3 z-[5] transition-opacity duration-200 " +
          (canNext ? "opacity-100" : "opacity-0")
        }
      >
        <div
          className="h-full w-full border-r border-black/15 bg-gradient-to-l from-black/10 to-transparent"
          style={{
            WebkitMaskImage:
              "linear-gradient(to bottom, transparent 0%, white 30%, white 70%, transparent 100%)",
            maskImage:
              "linear-gradient(to bottom, transparent 0%, white 30%, white 70%, transparent 100%)",
          }}
        />
      </div>
      {canPrev && (
        <button
          aria-label="Previous"
          className="absolute left-2 top-1/2 -translate-y-1/2 z-10 inline-flex items-center justify-center h-8 w-8 rounded-full bg-white text-black shadow-lg ring ring-black/5 hover:bg-white"
          onClick={() => emblaApi && emblaApi.scrollPrev()}
          type="button"
        >
          <ArrowLeft
            strokeWidth={1.5}
            className="h-4.5 w-4.5"
            aria-hidden="true"
          />
        </button>
      )}
      {canNext && (
        <button
          aria-label="Next"
          className="absolute right-2 top-1/2 -translate-y-1/2 z-10 inline-flex items-center justify-center h-8 w-8 rounded-full bg-white text-black shadow-lg ring ring-black/5 hover:bg-white"
          onClick={() => emblaApi && emblaApi.scrollNext()}
          type="button"
        >
          <ArrowRight
            strokeWidth={1.5}
            className="h-4.5 w-4.5"
            aria-hidden="true"
          />
        </button>
      )}
    </div>
  );
}

createRoot(document.getElementById("pizzaz-carousel-root")).render(<App />);

export default function PlaceCard({ place }) {
  if (!place) return null;
  return (
    <div className="min-w-[220px] select-none max-w-[220px] w-[65vw] sm:w-[220px] self-stretch flex flex-col">
      <div className="w-full">
        <img
          src={place.thumbnail}
          alt={place.name}
          className="w-full aspect-square rounded-2xl object-cover ring ring-black/5 shadow-[0px_2px_6px_rgba(0,0,0,0.06)]"
        />
      </div>
      <div className="mt-3 flex flex-col flex-1 flex-auto">
        <div className="text-base font-medium truncate line-clamp-1">
          {place.name}
        </div>
        <div className="text-xs mt-1 text-black/60 flex items-center gap-1">
          <Star className="h-3 w-3" aria-hidden="true" />
          {place.rating?.toFixed ? place.rating.toFixed(1) : place.rating}
          {place.price ? <span>· {place.price}</span> : null}
          <span>· San Francisco</span>
        </div>
        {place.description ? (
          <div className="text-sm mt-2 text-black/80 flex-auto">
            {place.description}
          </div>
        ) : null}
        <div className="mt-5">
          <button
            type="button"
            className="cursor-pointer inline-flex items-center rounded-full bg-[#F46C21] text-white px-4 py-1.5 text-sm font-medium hover:opacity-90 active:opacity-100"
          >
            Order now
          </button>
        </div>
      </div>
    </div>
  );
}
Pizzaz List Source
Screenshot of the Pizzaz list component

This list layout mirrors what you might embed in a chat-initiated itinerary or report. It balances a hero summary with a scrollable ranking so you can experiment with denser information hierarchies inside a component.

import React from "react";
import { createRoot } from "react-dom/client";
import markers from "../pizzaz/markers.json";
import { PlusCircle, Star } from "lucide-react";

function App() {
  const places = markers?.places || [];

  return (
    <div className="antialiased w-full text-black px-4 pb-2 border border-black/10 dark:border-white/10 rounded-2xl sm:rounded-3xl overflow-hidden">
      <div className="max-w-full">
        <div className="flex flex-row items-center gap-4 sm:gap-4 border-b border-black/5 py-4">
          <div
            className="sm:w-18 w-16 aspect-square rounded-xl bg-cover bg-center"
            style={{
              backgroundImage:
                "url(https://plus.unsplash.com/premium_photo-1675884306775-a0db978623a0?w=900&auto=format&fit=crop&q=60&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxzZWFyY2h8NDV8fHBpenphJTIwd2FsbHBhcGVyfGVufDB8fDB8fHww)",
            }}
          ></div>
          <div>
            <div className="text-base sm:text-xl font-medium">
              National Best Pizza List
            </div>
            <div className="text-sm text-black/60">
              A ranking of the best pizzerias in the world
            </div>
          </div>
          <div className="flex-auto hidden sm:flex justify-end pr-2">
            <button
              type="button"
              className="cursor-pointer inline-flex items-center rounded-full bg-[#F46C21] text-white px-4 py-1.5 sm:text-md text-sm font-medium hover:opacity-90 active:opacity-100"
            >
              Save List
            </button>
          </div>
        </div>
        <div className="min-w-full text-sm flex flex-col">
          {places.slice(0, 7).map((place, i) => (
            <div
              key={place.id}
              className="px-3 -mx-2 rounded-2xl hover:bg-black/5"
            >
              <div
                style={{
                  borderBottom:
                    i === 7 - 1 ? "none" : "1px solid rgba(0, 0, 0, 0.05)",
                }}
                className="flex w-full items-center hover:border-black/0! gap-2"
              >
                <div className="py-3 pr-3 min-w-0 w-full sm:w-3/5">
                  <div className="flex items-center gap-3">
                    <img
                      src={place.thumbnail}
                      alt={place.name}
                      className="h-10 w-10 sm:h-11 sm:w-11 rounded-lg object-cover ring ring-black/5"
                    />
                    <div className="w-3 text-end sm:block hidden text-sm text-black/40">
                      {i + 1}
                    </div>
                    <div className="min-w-0 sm:pl-1 flex flex-col items-start h-full">
                      <div className="font-medium text-sm sm:text-md truncate max-w-[40ch]">
                        {place.name}
                      </div>
                      <div className="mt-1 sm:mt-0.25 flex items-center gap-3 text-black/70 text-sm">
                        <div className="flex items-center gap-1">
                          <Star
                            strokeWidth={1.5}
                            className="h-3 w-3 text-black"
                          />
                          <span>
                            {place.rating?.toFixed
                              ? place.rating.toFixed(1)
                              : place.rating}
                          </span>
                        </div>
                        <div className="whitespace-nowrap sm:hidden">
                          {place.city || "–"}
                        </div>
                      </div>
                    </div>
                  </div>
                </div>
                <div className="hidden sm:block text-end py-2 px-3 text-sm text-black/60 whitespace-nowrap flex-auto">
                  {place.city || "–"}
                </div>
                <div className="py-2 whitespace-nowrap flex justify-end">
                  <PlusCircle strokeWidth={1.5} className="h-5 w-5" />
                </div>
              </div>
            </div>
          ))}
          {places.length === 0 && (
            <div className="py-6 text-center text-black/60">
              No pizzerias found.
            </div>
          )}
        </div>
        <div className="sm:hidden px-0 pt-2 pb-2">
          <button
            type="button"
            className="w-full cursor-pointer inline-flex items-center justify-center rounded-full bg-[#F46C21] text-white px-4 py-2 font-medium hover:opacity-90 active:opacity-100"
          >
            Save List
          </button>
        </div>
      </div>
    </div>
  );
}

createRoot(document.getElementById("pizzaz-list-root")).render(<App />);
Pizzaz Video Source
The video component wraps a scripted player that tracks playback, overlays controls, and reacts to fullscreen changes. Use it as a reference for media-heavy experiences that still need to integrate with the ChatGPT container APIs.

import { Maximize2, Play } from "lucide-react";
import React from "react";
import { createRoot } from "react-dom/client";
import { useMaxHeight } from "../use-max-height";
import { useOpenaiGlobal } from "../use-openai-global";
import script from "./script.json";

function App() {
  return (
    <div className="antialiased w-full text-black">
      <VideoPlayer />
    </div>
  );
}

createRoot(document.getElementById("pizzaz-video-root")).render(<App />);


export default function VideoPlayer() {
  const videoRef = React.useRef(null);
  const [showControls, setShowControls] = React.useState(false);
  const [showOverlayPlay, setShowOverlayPlay] = React.useState(true);
  const [isPlaying, setIsPlaying] = React.useState(false);
  const lastBucketRef = React.useRef(null);
  const isPlayingRef = React.useRef(false);
  const [activeTab, setActiveTab] = React.useState("summary");
  const [currentTime, setCurrentTime] = React.useState(0);

  const VIDEO_DESCRIPTION =
    "President Obama delivered his final weekly address thanking the American people for making him a better President and a better man.";

  const displayMode = useOpenaiGlobal("displayMode");
  const isFullscreen = displayMode === "fullscreen";
  const maxHeight = useMaxHeight() ?? undefined;

  const timeline = React.useMemo(() => {
    function toSeconds(ts) {
      if (!ts) return 0;
      const parts = String(ts).split(":");
      const [mm, ss] = parts.length === 2 ? parts : ["0", "0"];
      const m = Number(mm) || 0;
      const s = Number(ss) || 0;
      return m * 60 + s;
    }
    return Array.isArray(script)
      ? script.map((item) => ({
          start: toSeconds(item.start),
          end: toSeconds(item.end),
          description: item.description || "",
        }))
      : [];
  }, []);

  function formatSeconds(totalSeconds) {
    const total = Math.max(0, Math.floor(Number(totalSeconds) || 0));
    const minutes = Math.floor(total / 60);
    const seconds = total % 60;
    const pad = (n) => String(n).padStart(2, "0");
    return `${pad(minutes)}:${pad(seconds)}`;
  }

  const findDescriptionForTime = React.useCallback(
    (t) => {
      for (let i = 0; i < timeline.length; i++) {
        const seg = timeline[i];
        if (t >= seg.start && t < seg.end) {
          return seg.description || "";
        }
      }
      return "";
    },
    [timeline]
  );

  const sendDescriptionForTime = React.useCallback(
    (t, { force } = { force: false }) => {
      const bucket = Math.floor(Number(t || 0) / 10);
      if (!force && bucket === lastBucketRef.current) return;
      lastBucketRef.current = bucket;
      const desc = findDescriptionForTime(Number(t || 0));
      if (
        typeof window !== "undefined" &&
        window.oai &&
        window.oai.widget &&
        typeof window.oai.widget.setState === "function"
      ) {
        window.oai.widget.setState({
          currentSceneDescription: desc,
          videoDescription: VIDEO_DESCRIPTION,
        });
      }
    },
    [findDescriptionForTime]
  );

  async function handlePlayClick() {
    setShowOverlayPlay(false);
    setShowControls(true);
    try {
      if (displayMode === "inline") {
        await window?.openai?.requestDisplayMode?.({ mode: "pip" });
      }
    } catch {}
    try {
      await videoRef.current?.play?.();
    } catch {}
  }

  React.useEffect(() => {
    const el = videoRef.current;
    if (!el) return;

    function handlePlay() {
      setIsPlaying(true);
      isPlayingRef.current = true;
      // Immediate update on play
      sendDescriptionForTime(el.currentTime, { force: true });
      setCurrentTime(el.currentTime);
    }

    function handlePause() {
      setIsPlaying(false);
      isPlayingRef.current = false;
    }

    function handleEnded() {
      setIsPlaying(false);
      isPlayingRef.current = false;
    }

    function handleTimeUpdate() {
      if (!isPlayingRef.current) return;
      sendDescriptionForTime(el.currentTime);
      setCurrentTime(el.currentTime);
    }

    function handleSeeking() {
      // Update immediately while user scrubs or jumps
      sendDescriptionForTime(el.currentTime, { force: true });
      setCurrentTime(el.currentTime);
    }

    function handleSeeked() {
      // Ensure we reflect the final position after seek completes
      sendDescriptionForTime(el.currentTime, { force: true });
      setCurrentTime(el.currentTime);
    }

    el.addEventListener("play", handlePlay);
    el.addEventListener("pause", handlePause);
    el.addEventListener("ended", handleEnded);
    el.addEventListener("timeupdate", handleTimeUpdate);
    el.addEventListener("seeking", handleSeeking);
    el.addEventListener("seeked", handleSeeked);

    return () => {
      el.removeEventListener("play", handlePlay);
      el.removeEventListener("pause", handlePause);
      el.removeEventListener("ended", handleEnded);
      el.removeEventListener("timeupdate", handleTimeUpdate);
      el.removeEventListener("seeking", handleSeeking);
      el.removeEventListener("seeked", handleSeeked);
    };
  }, [sendDescriptionForTime]);

  // If the host returns the component to inline mode, pause and show the overlay play button
  React.useEffect(() => {
    if (displayMode !== "inline") return;
    try {
      videoRef.current?.pause?.();
    } catch {}
    setIsPlaying(false);
    isPlayingRef.current = false;
    setShowControls(false);
    setShowOverlayPlay(true);
  }, [displayMode]);

  return (
    <div
      className="relative w-full bg-white group"
      style={{ aspectRatio: "16 / 9", maxHeight }}
    >
      <div
        className={
          isFullscreen
            ? "flex flex-col lg:flex-row w-full h-full gap-4 p-4"
            : "w-full h-full"
        }
      >
        {/* Left: Video */}
        <div
          className={
            isFullscreen ? "relative flex-1 h-full" : "relative w-full h-full"
          }
        >
          <div style={{ aspectRatio: "16 / 9" }} className="relative w-full">
            <video
              ref={videoRef}
              className={
                "absolute inset-0 w-full h-auto" +
                (isFullscreen ? " shadow-lg rounded-3xl" : "")
              }
              controls={showControls}
              playsInline
              preload="metadata"
              aria-label="How to make pizza"
            >
              <source
                src="https://obamawhitehouse.archives.gov/videos/2017/January/20170114_Weekly_Address_HD.mp4#t=8"
                type="video/mp4"
              />
              Your browser does not support the video tag.
            </video>

            <div className="absolute inset-0 z-10 flex items-center justify-center pointer-events-none">
              {showOverlayPlay && (
                <button
                  type="button"
                  aria-label="Play video"
                  className="h-20 w-20 backdrop-blur-xl bg-black/40 ring ring-black/20 shadow-xl rounded-full text-white flex items-center justify-center transition pointer-events-auto"
                  onClick={handlePlayClick}
                >
                  <Play
                    strokeWidth={1.5}
                    className="h-10 w-10"
                    aria-hidden="true"
                  />
                </button>
              )}
            </div>
          </div>

          {displayMode !== "fullscreen" && (
            <button
              aria-label="Enter fullscreen"
              className="absolute top-3 right-3 z-20 rounded-full bg-black/30 backdrop-blur-2xl text-white p-2 pointer-events-auto"
              onClick={() => {
                if (
                  displayMode !== "fullscreen" &&
                  window?.openai?.requestDisplayMode
                ) {
                  window.openai.requestDisplayMode({ mode: "fullscreen" });
                }
              }}
            >
              <Maximize2
                strokeWidth={1.5}
                className="h-4.5 w-4.5"
                aria-hidden="true"
              />
            </button>
          )}

          {/* Hover title overlay (hidden in fullscreen) */}
          {!isFullscreen && (
            <div className="absolute left-2 right-0 bottom-18 pointer-events-none flex justify-start">
              <div className="text-white px-3 py-1 transition-opacity duration-150 opacity-0 group-hover:opacity-100">
                <div className="text-sm font-medium text-white/60">
                  Weekly Address
                </div>
                <div className="text-2xl font-medium">
                  The Honor of Serving You as President
                </div>
              </div>
            </div>
          )}
        </div>

        {/* Right: Details panel (fullscreen only) */}
        {isFullscreen && (
          <div className="w-full lg:w-[364px] px-4 h-full flex flex-col">
            <div className="text-sm mt-4 text-black/60">Weekly Address</div>
            <div className="text-3xl leading-tighter font-medium text-black mt-4">
              The Honor of Serving You as President
            </div>
            <div className="mt-4 flex items-center gap-3 text-sm text-black/70">
              <img
                src="https://upload.wikimedia.org/wikipedia/commons/8/8d/President_Barack_Obama.jpg"
                alt="Barack Obama portrait"
                className="h-8 translate-y-[1px] w-8 rounded-full object-cover ring-1 ring-black/10"
              />
              <div className="flex flex-col h-full">
                <span className="text-sm font-medium">President Obama</span>
                <span className="text-sm text-black/60">January 13, 2017</span>
              </div>
            </div>

            <div className="mt-8 inline-flex rounded-full bg-black/5 p-1">
              <button
                type="button"
                className={
                  "px-3 py-1.5 text-sm font-medium rounded-full flex-auto transition " +
                  (activeTab === "summary"
                    ? "bg-white shadow text-black"
                    : "text-black/60 hover:text-black")
                }
                onClick={() => setActiveTab("summary")}
              >
                Summary
              </button>
              <button
                type="button"
                className={
                  "ml-1 px-3 py-1.5 font-medium text-sm flex-auto rounded-full transition " +
                  (activeTab === "transcript"
                    ? "bg-white shadow text-black"
                    : "text-black/60 hover:text-black")
                }
                onClick={() => setActiveTab("transcript")}
              >
                Transcript
              </button>
            </div>

            <div
              className="mt-5 text-sm overflow-auto pb-32 text-black/80"
              style={{
                WebkitMaskImage:
                  "linear-gradient(to bottom, black 75%, rgba(0,0,0,0) 100%)",
                maskImage:
                  "linear-gradient(to bottom, black 75%, rgba(0,0,0,0) 100%)",
              }}
            >
              {activeTab === "summary" ? (
                <p>
                  <p>
                    This week, President Obama delivered his final weekly
                    address thanking the American people for making him a better
                    President and a better man. Over the past eight years, we
                    have seen the goodness, resilience, and hope of the American
                    people. We’ve seen what’s possible when we come together in
                    the hard, but vital work of self-government – but we can’t
                    take our democracy for granted. Our success as a Nation
                    depends on our participation.
                  </p>
                  <p className="mt-6">
                    It’s up to all of us to be guardians of our democracy, and
                    to embrace the task of continually trying to improve our
                    Nation. Despite our differences, we all share the same
                    title: Citizen. And that is why President Obama looks
                    forward to working by your side, as a citizen, for all of
                    his remaining days.
                  </p>
                </p>
              ) : (
                <div>
                  {timeline.map((seg, idx) => {
                    const isActive =
                      currentTime >= seg.start && currentTime < seg.end;
                    return (
                      <p
                        key={idx}
                        className={
                          "px-2 py-1 rounded-md my-0.5 transition-colors transition-opacity duration-300 flex items-start gap-2 " +
                          (isActive
                            ? "bg-black/5 opacity-100"
                            : "bg-transparent opacity-80")
                        }
                      >
                        <span className="text-xs text-black/40 tabular-nums leading-5 mt-0.5 mr-1">
                          {formatSeconds(seg.start)}
                        </span>
                        <span className="flex-1">{seg.description}</span>
                      </p>
                    );
                  })}
                </div>
              )}
            </div>
          </div>
        )}
      </div>
    </div>
  );
}

import React from "react";
import { motion } from "framer-motion";
import { Star, X } from "lucide-react";

export default function Inspector({ place, onClose }) {
  if (!place) return null;
  return (
    <motion.div
      key={place.id}
      initial={{ opacity: 0, scale: 0.95 }}
      animate={{ opacity: 1, scale: 1 }}
      exit={{ opacity: 0, scale: 0.95 }}
      transition={{ type: "spring", bounce: 0, duration: 0.25 }}
      className="pizzaz-inspector absolute inset-0 z-30 w-full lg:absolute lg:inset-auto lg:top-8 lg:bottom-8 lg:right-8 lg:z-20 lg:w-[360px] lg:max-w-[75%] pointer-events-auto"
    >
    <button
      aria-label="Close details"
      className="hidden lg:inline-flex absolute z-10 top-4 left-4 rounded-full p-2 bg-white ring ring-black/5 shadow-2xl hover:bg-white"
      onClick={onClose}
    >
      <X className="h-[18px] w-[18px]" aria-hidden="true" />
    </button>
      <div className="relative h-full overflow-y-auto rounded-none lg:rounded-3xl bg-white text-black shadow-xl ring ring-black/10">
        <div className="relative">
          <img
            src={place.thumbnail}
            alt={place.name}
            className="w-full h-80 object-cover rounded-none lg:rounded-t-2xl"
          />
        </div>

        <div className="h-[calc(100%-11rem)] sm:h-[calc(100%-14rem)]">
          <div className="p-4 sm:p-5">
            <div className="text-2xl font-medium truncate">{place.name}</div>
            <div className="text-sm mt-1 opacity-70 flex items-center gap-1">
              <Star className="h-3.5 w-3.5" aria-hidden="true" />
              {place.rating.toFixed(1)}
              {place.price ? <span>· {place.price}</span> : null}
              <span>· San Francisco</span>
            </div>
            <div className="mt-3 flex flex-row items-center gap-3 font-medium">
              <div className="rounded-full bg-[#F46C21] text-white cursor-pointer px-4 py-1.5">Order Online</div>
              <div className="rounded-full border border-[#F46C21]/50 text-[#F46C21] cursor-pointer  px-4 py-1.5">Contact</div>
            </div>
            <div className="text-sm mt-5">
              {place.description} Enjoy a slice at one of SF's favorites. Fresh ingredients, great crust, and cozy vibes.
            </div>
          </div>

          <div className="px-4 sm:px-5 pb-4">
            <div className="text-lg font-medium mb-2">Reviews</div>
            <ul className="space-y-3 divide-y divide-black/5">
              {[
                {
                  user: "Alex M.",
                  avatar: "https://i.pravatar.cc/40?img=3",
                  text: "Fantastic crust and balanced toppings. The marinara is spot on!",
                },
                {
                  user: "Priya S.",
                  avatar: "https://i.pravatar.cc/40?img=5",
                  text: "Cozy vibe and friendly staff. Quick service on a Friday night.",
                },
                {
                  user: "Jordan R.",
                  avatar: "https://i.pravatar.cc/40?img=8",
                  text: "Great for sharing. Will definitely come back with friends.",
                },
              ].map((review, idx) => (
                <li key={idx} className="py-3">
                  <div className="flex items-start gap-3">
                    <img
                      src={review.avatar}
                      alt={`${review.user} avatar`}
                      className="h-8 w-8 ring ring-black/5 rounded-full object-cover flex-none"
                    />
                    <div className="min-w-0 gap-1 flex flex-col">
                      <div className="text-xs font-medium text-black/70">{review.user}</div>
                      <div className="text-sm">{review.text}</div>
                    </div>
                  </div>
                </li>
              ))}
            </ul>
          </div>
        </div>
      </div>
    </motion.div>
  );
}


import React from "react";
import useEmblaCarousel from "embla-carousel-react";
import { useOpenaiGlobal } from "../use-openai-global";
import { Filter, Settings2, Star } from "lucide-react";

function PlaceListItem({ place, isSelected, onClick }) {
  return (
    <div
      className={
        "rounded-2xl px-3 select-none hover:bg-black/5 cursor-pointer" +
        (isSelected ? " bg-black/5" : "")
      }
    >
      <div
        className={`border-b ${
          isSelected ? "border-black/0" : "border-black/5"
        } hover:border-black/0`}
      >
        <button
          className="w-full text-left py-3 transition flex gap-3 items-center"
          onClick={onClick}
        >
          <img
            src={place.thumbnail}
            alt={place.name}
            className="h-16 w-16 rounded-lg object-cover flex-none"
          />
          <div className="min-w-0">
            <div className="font-medium truncate">{place.name}</div>
            <div className="text-xs text-black/50 truncate">
              {place.description}
            </div>
            <div className="text-xs mt-1 text-black/50 flex items-center gap-1">
              <Star className="h-3 w-3" aria-hidden="true" />
              {place.rating.toFixed(1)}
              {place.price ? <span className="">· {place.price}</span> : null}
            </div>
          </div>
        </button>
      </div>
    </div>
  );
}

export default function Sidebar({ places, selectedId, onSelect }) {
  const [emblaRef] = useEmblaCarousel({ dragFree: true, loop: false });
  const displayMode = useOpenaiGlobal("displayMode");
  const forceMobile = displayMode !== "fullscreen";

  return (
    <>
      {/* Desktop/Tablet sidebar */}
      <div className={`${forceMobile ? "hidden" : "hidden md:block"} absolute inset-y-0 left-0 z-20 w-[340px] max-w-[75%] pointer-events-auto`}>
        <div className="px-2 h-full overflow-y-auto bg-white text-black">
          <div className="flex justify-between flex-row items-center px-3 sticky bg-white top-0 py-4 text-md font-medium">
            {places.length} results
            <div>
              <Settings2 className="h-5 w-5" aria-hidden="true" />
            </div>
          </div>
          <div>
            {places.map((place) => (
              <PlaceListItem
                key={place.id}
                place={place}
                isSelected={displayMode === "fullscreen" && selectedId === place.id}
                onClick={() => onSelect(place)}
              />
            ))}
          </div>
        </div>
      </div>

      {/* Mobile bottom carousel */}
      <div className={`${forceMobile ? "" : "md:hidden"} absolute inset-x-0 bottom-0 z-20 pointer-events-auto`}>
        <div className="pt-2 text-black">
          <div className="overflow-hidden" ref={emblaRef}>
            <div className="px-3 py-3 flex gap-3">
              {places.map((place) => (
                <div className="ring ring-black/10 max-w-[330px] w-full shadow-xl rounded-2xl bg-white">
                  <PlaceListItem
                    key={place.id}
                    place={place}
                    isSelected={displayMode === "fullscreen" && selectedId === place.id}
                    onClick={() => onSelect(place)}
                  />
                </div>
              ))}
            </div>
          </div>
        </div>
      </div>
    </>
  );
}

{
  "places": [
    {
      "id": "tonys-pizza-napoletana",
      "name": "Tony's Pizza Napoletana",
      "coords": [-122.4098, 37.8001],
      "description": "Award‑winning Neapolitan pies in North Beach.",
      "city": "North Beach",
      "rating": 4.8,
      "price": "$$$",
      "thumbnail": "https://images.unsplash.com/photo-1513104890138-7c749659a591?q=80&w=2670&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D"
    },
    {
      "id": "golden-boy-pizza",
      "name": "Golden Boy Pizza",
      "coords": [-122.4093, 37.7990],
      "description": "Focaccia‑style squares, late‑night favorite.",
      "city": "North Beach",
      "rating": 4.6,
      "price": "$",
      "thumbnail": "https://plus.unsplash.com/premium_photo-1661762555601-47d088a26b50?w=900&auto=format&fit=crop&q=60&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxzZWFyY2h8OXx8cGl6emF8ZW58MHx8MHx8fDA%3D"
    },
    {
      "id": "pizzeria-delfina-mission",
      "name": "Pizzeria Delfina (Mission)",
      "coords": [-122.4255, 37.7613],
      "description": "Thin‑crust classics on 18th Street.",
      "city": "Mission",
      "rating": 4.5,
      "price": "$$",
      "thumbnail": "https://images.unsplash.com/photo-1571997478779-2adcbbe9ab2f?w=900&auto=format&fit=crop&q=60&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxzZWFyY2h8OHx8cGl6emF8ZW58MHx8MHx8fDA%3D"
    },
    {
      "id": "little-star-divisadero",
      "name": "Little Star Pizza",
      "coords": [-122.4388, 37.7775],
      "description": "Deep‑dish and cornmeal crust favorites.",
      "city": "Alamo Square",
      "rating": 4.5,
      "price": "$$",
      "thumbnail": "https://images.unsplash.com/photo-1579751626657-72bc17010498?w=900&auto=format&fit=crop&q=60&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxzZWFyY2h8MTB8fHBpenphfGVufDB8fDB8fHww"
    },
    {
      "id": "il-casaro-columbus",
      "name": "Il Casaro Pizzeria",
      "coords": [-122.4077, 37.7990],
      "description": "Wood‑fired pies and burrata in North Beach.",
      "city": "North Beach",
      "rating": 4.6,
      "price": "$$",
      "thumbnail": "https://images.unsplash.com/photo-1594007654729-407eedc4be65?w=900&auto=format&fit=crop&q=60&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxzZWFyY2h8MTF8fHBpenphfGVufDB8fDB8fHww"
    },
    {
      "id": "capos",
      "name": "Capo's",
      "coords": [-122.4097, 37.7992],
      "description": "Chicago‑style pies from Tony Gemignani.",
      "city": "North Beach",
      "rating": 4.4,
      "price": "$$$",
      "thumbnail": "https://images.unsplash.com/photo-1593560708920-61dd98c46a4e?w=900&auto=format&fit=crop&q=60&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxzZWFyY2h8MTV8fHBpenphfGVufDB8fDB8fHww"
    },
    {
      "id": "ragazza",
      "name": "Ragazza",
      "coords": [-122.4380, 37.7722],
      "description": "Neighborhood spot with seasonal toppings.",
      "city": "Lower Haight",
      "rating": 4.4,
      "price": "$$",
      "thumbnail": "https://images.unsplash.com/photo-1600028068383-ea11a7a101f3?w=900&auto=format&fit=crop&q=60&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxzZWFyY2h8MTl8fHBpenphfGVufDB8fDB8fHww"
    },
    {
      "id": "del-popolo",
      "name": "Del Popolo",
      "coords": [-122.4123, 37.7899],
      "description": "Sourdough, wood‑fired pies near Nob Hill.",
      "city": "Nob Hill",
      "rating": 4.6,
      "price": "$$$",
      "thumbnail": "https://images.unsplash.com/photo-1574071318508-1cdbab80d002?w=900&auto=format&fit=crop&q=60&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxzZWFyY2h8MjZ8fHBpenphfGVufDB8fDB8fHww"
    },
    {
      "id": "square-pie-guys",
      "name": "Square Pie Guys",
      "coords": [-122.4135, 37.7805],
      "description": "Crispy‑edged Detroit‑style in SoMa.",
      "city": "SoMa",
      "rating": 4.5,
      "price": "$$",
      "thumbnail": "https://images.unsplash.com/photo-1589187151053-5ec8818e661b?w=900&auto=format&fit=crop&q=60&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxzZWFyY2h8Mzl8fHBpenphfGVufDB8fDB8fHww"
    },
    {
      "id": "zero-zero",
      "name": "Zero Zero",
      "coords": [-122.4019, 37.7818],
      "description": "Bianca pies and cocktails near Yerba Buena.",
      "city": "Yerba Buena",
      "rating": 4.3,
      "price": "$$",
      "thumbnail": "https://plus.unsplash.com/premium_photo-1674147605295-53b30e11d8c0?w=900&auto=format&fit=crop&q=60&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxzZWFyY2h8NDF8fHBpenphfGVufDB8fDB8fHww"
    }
  ]
}

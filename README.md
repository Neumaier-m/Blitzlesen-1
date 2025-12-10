<!DOCTYPE html>
<html lang="de">
<head>
  <meta charset="UTF-8" />
  <title>Blitzlesen</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <style>
    * {
      box-sizing: border-box;
      font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
    }

    body {
      margin: 0;
      padding: 0;
      background: #f4f4f5;
      display: flex;
      justify-content: center;
      align-items: flex-start;
      min-height: 100vh;
    }

    .app {
      background: #ffffff;
      margin: 1.5rem;
      padding: 1.5rem;
      border-radius: 1rem;
      max-width: 700px;
      width: 100%;
      box-shadow: 0 10px 25px rgba(0, 0, 0, 0.07);
    }

    h1 {
      margin-top: 0;
      font-size: 1.8rem;
      text-align: center;
    }

    .meta {
      display: flex;
      flex-wrap: wrap;
      gap: 0.75rem;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 1rem;
      font-size: 0.95rem;
    }

    .badge {
      display: inline-flex;
      align-items: center;
      padding: 0.25rem 0.6rem;
      border-radius: 999px;
      background: #eef2ff;
      font-size: 0.85rem;
    }

    .badge span {
      font-weight: 600;
      margin-left: 0.25rem;
    }

    button {
      border: none;
      border-radius: 999px;
      padding: 0.5rem 1.1rem;
      font-size: 0.95rem;
      cursor: pointer;
      display: inline-flex;
      align-items: center;
      gap: 0.35rem;
      transition: transform 0.05s ease, box-shadow 0.1s ease, background 0.15s ease;
    }

    button.primary {
      background: #4f46e5;
      color: #ffffff;
      box-shadow: 0 4px 12px rgba(79, 70, 229, 0.35);
    }

    button.primary:disabled {
      background: #a5b4fc;
      cursor: default;
      box-shadow: none;
      transform: none;

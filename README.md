<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Decipher Cue - Stable Multi-Tenant Master Blueprint</title>
    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: system-ui, -apple-system, sans-serif; }
        body { background: #f0f2f5; color: #1f2937; min-height: 100vh; overflow-y: scroll; padding-bottom: 80px; }
        #master-status-strip { background: #10b981; color: #ffffff; text-align: center; padding: 14px; font-weight: bold; font-size: 0.95rem; position: sticky; top: 0; z-index: 9999; }
        .main-container { max-width: 1200px; margin: 30px auto; padding: 0 20px; display: flex; flex-direction: column; gap: 45px; width: 100%; }
        .panel-section-wrapper { display: flex; flex-direction: column; width: 100%; background: #ffffff; border-radius: 8px; border: 1px solid #e5e7eb; overflow: hidden; box-shadow: 0 4px 6px rgba(0,0,0,0.02); }
        .section-header { background: #0f172a; color: #ffffff; padding: 16px 20px; font-size: 1.25rem; font-weight: bold; display: flex; justify-content: space-between; align-items: center; letter-spacing: 0.5px; }
        .section-body { padding: 25px; display: flex; flex-direction: column; gap: 20px; }
        .card { background: #f8fafc; padding: 20px; border-radius: 6px; border: 1px solid #e2e8f0; width: 100%; }
        .grid-2 { display: grid; grid-template-columns: 1fr 1fr; gap: 24px; width: 100%; }
        .grid-3 { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 20px; width: 100%; }
        .form-group { margin-bottom: 15px; display: flex; flex-direction: column; gap: 6px; width: 100%; }
        .form-control { padding: 12px; border: 1px solid #cbd5e1; border-radius: 6px; font-size: 1rem; width: 100%; background: #ffffff; color: #334155; margin-top: 5px; }
        .recorder-control-bar { background: #111827; padding: 15px; border-radius: 6px; display: flex; justify-content: space-between; align-items: center; border: 1px solid #1e293b; width: 100%; color: white; }
        .recording-status-ticker { color: #ef4444; font-weight: bold; font-family: monospace; font-size: 0.9rem; }
        .classroom-video-hub { display: grid; grid-template-columns: 2fr 1fr; gap: 20px; width: 100%; }
        .live-stream-theater { background: #1e293b; border-radius: 8px; padding: 25px; display: flex; flex-direction: column; justify-content: center; align-items: center; border: 2px solid #3b82f6; min-height: 240px; color: white; position: relative; text-align: center; }
        .live-broadcast-tag { position: absolute; top: 12px; left: 12px; background: #ef4444; color: white; padding: 4px 10px; border-radius: 4px; font-size: 0.75rem; font-weight: bold; }
        .voip-status-badge { background: #334155; padding: 20px; border-radius: 8px; text-align: center; border: 1px solid #475569; display: flex; flex-direction: column; align-items: center; justify-content: center; gap: 8px; color: white; }
        .voip-dot { width: 12px; height: 12px; background: #10b981; border-radius: 50%; display: inline-block; }
        .board-canvas-surface { background: #ffffff; border: 1px solid #cbd5e1; border-radius: 8px; padding: 15px; width: 100%; display: flex; flex-direction: column; gap: 10px; }
        .canvas-toolbar { display: flex; justify-content: space-between; align-items: center; background: #334155; padding: 10px; border-radius: 6px; color: white; }
        .canvas-interactive-sandbox { background: #ffffff; border: 2px dashed #94a3b8; height: 140px; border-radius: 6px; display: flex; flex-direction: column; justify-content: center; align-items: center; text-align: center; padding: 20px; color: #4b5563; font-weight: bold; }
        .cabinet-table { width: 100%; border-collapse: collapse; text-align: left; }
        .cabinet-table th, .cabinet-table td { padding: 10px 12px; border-bottom: 1px solid #e2e8f0; font-size: 0.85rem; }
        .cabinet-table th { background: #f1f5f9; font-weight: bold; color: #334155; }
        .ext-badge { display: inline-block; padding: 2px 5px; border-radius: 4px; font-size: 0.7rem; font-family: monospace; font-weight: bold; text-transform: uppercase; color: white; background: #2563eb; }
        .msg-display-panel { padding: 20px; border-radius: 6px; background: #ffffff; border: 1px solid #cbd5e1; display: flex; flex-direction: column; gap: 10px; width: 100%; }
        .msg-bubble { padding: 10px 14px; border-radius: 8px; max-width: 80%; font-size: 0.9rem; line-height: 1.4; }
        .msg-received { background: #f1f5f9; align-self: flex-start; color: #334155; border-left: 3px solid #64748b; }
        .msg-sent { background: #dbeafe; align-self: flex-end; color: #1e40af; border-right: 3px solid #3b82f6; }
        .encryption-notice { font-size: 0.75rem; color: #059669; font-weight: bold; font-family: monospace; margin-bottom: 4px; }
        .msg-attachment { display: flex; align-items: center; gap: 8px; margin-top: 6px; padding: 6px; background: #ffffff; border: 1px solid #cbd5e1; border-radius: 4px; font-size: 0.8rem; font-weight: bold; color: #334155; }
        .schedule-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 15px; margin-top: 15px; }
        .slot-btn-static { background: #ffffff; border: 1px solid #cbd5e1; padding: 15px; border-radius: 6px; text-align: center; display: flex; flex-direction: column; gap: 4px; border-top: 3px solid #2563eb; }
        .kids-corner-banner { background: #fffbeb; border-left: 5px solid #f59e0b; padding: 20px; border-radius: 6px; width: 100%; }
        .badge { display: inline-block; padding: 4px 8px; background: #1f2937; color: white; border-radius: 4px; font-size: 0.75rem; font-family: monospace; font-weight: bold; text-transform: uppercase; }
        .badge-kids { background: #10b981; } .badge-tbs { background: #dc2626; }
        .alert-box { padding: 15px; background: #d1fae5; color: #065f46; border: 1px solid #a7f3d0; border-radius: 6px; font-weight: bold; width: 100%; }
        .content-link-card { background: #ffffff; border: 1px solid #e2e8f0; border-radius: 8px; padding: 20px; display: flex; flex-direction: column; justify-content: space-between; border-top: 4px solid #2563eb; }
        .btn-outline-connected { background: #ffffff; border: 1px solid #10b981; text-align: left; width: 100%; color: #065f46; padding: 12px 15px; font-weight: bold; border-radius: 6px; margin-bottom: 8px; border-left: 4px solid #10b981; }
    </style>
</head>
<body>
    <div id="master-status-strip">UNIFIED SIMULATION TERMINAL ACTIVE: 100% Fully Closed Architecture Rendered Globally.</div>
    <div class="main-container">

        <!-- MODULE 1: USER PROFILE HUB -->
        <div class="panel-section-wrapper">
            <div class="section-header" style="border-left: 4px solid #2563eb;">
                <span>1. User Profile Hub & Course Content Infrastructure Matrix</span>
                <span style="font-size: 0.8rem; background: #2563eb; padding: 4px 10px; border-radius: 4px; font-weight: bold;">Profile Core</span>
            </div>
            <div class="section-body">
                <div class="grid-3">
                    <div class="card" style="background: #ffffff; border-top: 4px solid #0f172a; text-align: center; padding: 30px 20px;">
                        <div style="font-size: 3rem;">👤</div>
                        <h4 style="font-weight: bold; color: #0f172a;">Instructor Profile Hub</h4>
                        <p style="font-size: 0.85rem; color: #64748b;">Container Instance: Admin_Node_Alpha_01</p>
                        <div style="margin-top: 15px; background: #eff6ff; color: #2563eb; padding: 6px; border-radius: 4px; font-size: 0.8rem; font-weight: bold;">Clearance Level: Administrator</div>
                    </div>
                    <div class="content-link-card">
                        <div>
                            <span class="badge">Syllabus Track 1</span>
                            <h4 style="font-weight: bold; font-size: 1.05rem; margin-top: 6px;">NIST NICE Security Captures</h4>
                            <p style="color: #64748b; font-size: 0.85rem; margin-top: 4px;">Active training syllabus data mapping system log controls matching U.S. institutional standards.</p>
                        </div>
                        <div style="margin-top: 15px; background: #e0f2fe; color: #0369a1; padding: 8px; border-radius: 4px; font-size: 0.8rem; font-weight: bold; text-align: center;">Pipelined to Education Corner</div>
                    </div>
                    <div class="content-link-card">
                        <div>
                            <span class="badge">Syllabus Track 2</span>
                            <h4 style="font-weight: bold; font-size: 1.05rem; margin-top: 6px;">FAA Part 147 Airframe Blueprints</h4>
                            <p style="color: #64748b; font-size: 0.85rem; margin-top: 4px;">Aviation structure hour records, sheet metal repair layouts, and fluid thermodynamic calculations.</p>
                        </div>
                        <div style="margin-top: 15px; background: #e0f2fe; color: #0369a1; padding: 8px; border-radius: 4px; font-size: 0.8rem; font-weight: bold; text-align: center;">Pipelined to Education Corner</div>
                    </div>
                </div>
            </div>
        </div>

        <!-- MODULE 2: LOCAL SECURE FILE CABINET -->
        <div class="panel-section-wrapper">
            <div class="section-header" style="border-left: 4px solid #8b5cf6;">
                <span>2. Secure Multi-Tenant Local File Cabinet Module</span>
                <span style="font-size: 0.8rem; background: #8b5cf6; padding: 4px 10px; border-radius: 4px; font-weight: bold;">File Vault</span>
            </div>
            <div class="section-body">

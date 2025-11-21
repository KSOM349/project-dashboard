# project-dashboard
Hela Projektet - Komplett Dashboard
<!-- أضف هذا القسم قبل </body> -->
<section id="team-collaboration" class="section" style="background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); color: white;">
    <div class="section-header">
        <h2>👥 توثيق الفريق والواجبات</h2>
    </div>
    
    <div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); gap: 2rem; margin-top: 2rem;">
        <!-- نموذج إضافة واجب -->
        <div style="background: rgba(255,255,255,0.1); padding: 2rem; border-radius: 15px; backdrop-filter: blur(10px);">
            <h3>📝 إضافة واجب جديد</h3>
            <form id="assignment-form" onsubmit="addAssignment(event)">
                <input type="text" id="task-name" placeholder="اسم الواجب" required 
                       style="width: 100%; padding: 12px; margin: 10px 0; border: none; border-radius: 8px;">
                <textarea id="task-desc" placeholder="وصف الواجب" required
                         style="width: 100%; padding: 12px; margin: 10px 0; border: none; border-radius: 8px; height: 100px;"></textarea>
                <select id="task-assignee" style="width: 100%; padding: 12px; margin: 10px 0; border: none; border-radius: 8px;">
                    <option value="Kaled Osman">Kaled Osman</option>
                    <option value="Fahad Hussain">Fahad Hussain</option>
                    <option value="Stefan Österberg">Stefan Österberg</option>
                    <option value="Marcus Tibell">Marcus Tibell</option>
                    <option value="Jens Annell">Jens Annell</option>
                    <option value="Luwam">Luwam</option>
                </select>
                <button type="submit" style="width: 100%; padding: 12px; background: #10b981; color: white; border: none; border-radius: 8px; cursor: pointer;">
                    ➕ إضافة الواجب
                </button>
            </form>
        </div>

        <!-- قائمة الواجبات -->
        <div style="background: rgba(255,255,255,0.1); padding: 2rem; border-radius: 15px; backdrop-filter: blur(10px);">
            <h3>📋 الواجبات الحالية</h3>
            <div id="assignments-list">
                <div style="background: rgba(255,255,255,0.2); padding: 1rem; margin: 10px 0; border-radius: 8px;">
                    <strong>تحسين خوارزمية Dijkstra</strong>
                    <p>إضافة تحسينات على الأداء</p>
                    <small>المسؤول: Kaled Osman</small>
                </div>
            </div>
        </div>
    </div>
</section>

<script>
function addAssignment(event) {
    event.preventDefault();
    
    const name = document.getElementById('task-name').value;
    const desc = document.getElementById('task-desc').value;
    const assignee = document.getElementById('task-assignee').value;
    
    const assignment = document.createElement('div');
    assignment.style.background = 'rgba(255,255,255,0.2)';
    assignment.style.padding = '1rem';
    assignment.style.margin = '10px 0';
    assignment.style.borderRadius = '8px';
    
    assignment.innerHTML = `
        <strong>${name}</strong>
        <p>${desc}</p>
        <small>المسؤول: ${assignee}</small>
    `;
    
    document.getElementById('assignments-list').appendChild(assignment);
    
    // Reset form
    document.getElementById('assignment-form').reset();
    
    alert('✅ تم إضافة الواجب بنجاح!');
}
</script>

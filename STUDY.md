<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>课程列表与互动</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Dancing+Script&family=Roboto:wght@700&display=swap');

        body {
            font-family: Arial, sans-serif;
            font-family: 'Dancing Script', cursive;
            margin: 0;
            padding: 0;
            background-image: url('https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fc-ssl.duitang.com%2Fuploads%2Fitem%2F201702%2F06%2F20170206161300_CSe4U.thumb.1000_0.png&refer=http%3A%2F%2Fc-ssl.duitang.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1708680013&t=98cc7d3eec84de4a6c78b0c5c738b7f6');
            background-size: cover;
            background-attachment: fixed;
            background-color: rgba(255, 255, 255, 0.3); /* 更加虚化的背景 */
            display: flex;
            justify-content: space-around;
            align-items: center;
            height: 100vh;
        }

        .column {
            width:80%;
            max-width: 450px;
        }

        .column-title {
            font-size: 36px;
            text-align: center;
            margin-bottom: 20px;
            font-family: 'Dancing Script', cursive;
        }

        .alfie-column {
            font-family: 'Dancing Script', cursive;
        }

        .jenny-column {
            font-family: 'Roboto', sans-serif;
            font-weight: 1000;
        }

        .course-list {
            list-style-type: none;
            padding: 0;
        }

        .course-item {
            background-color: #fff;
            border: 1px solid #ddd;
            padding: 15px;
            margin-bottom: 15px;
            border-radius: 5px;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
            display: flex;
            align-items: center;
            justify-content: space-between;
        }

        .course-item h3 {
            margin: 0;
            font-size: 20px;
            color: #333;
            flex: 2; /* 分配空间，让名称有足够空间显示 */
            text-align: left; /* 对齐课程名称 */
        }

        .progress-container {
            display: flex;
            align-items: center;
            flex: 3; /* 分配更多空间给进度条 */
        }

        .progress {
            flex-grow: 1;
            height: 20px;
            background-color: #ddd;
            border-radius: 10px;
            overflow: hidden;
            margin: 0 10px;
            position: relative;
            width: 100%; /* 拉长进度条 */
        }

        .progress-bar {
            height: 100%;
            width: 0%;
            border-radius: 10px;
            transition: width 0.25s;
            position: absolute;
        }

        .course-level {
            width: 30px;
            text-align: center;
            font-size: 18px;
            font-weight: bold;
            color: black;
            margin: 0 10px;
        }

        .course-item button {
            background-color: #fcfcfc;
            border: none;
            padding: 5px 10px;
            margin-left: 5px;
            cursor: pointer;
            font-size: 20px;
        }

        /* 颜色设置 */
        .course-item:nth-child(odd) { background-color: #e1f5fe; }
        .course-item:nth-child(odd) .progress-bar { background-color: #03a9f4; }
        .course-item:nth-child(even) { background-color: #e8f5e9; }
        .course-item:nth-child(even) .progress-bar { background-color: #43a047; }
    </style>
</head>
<body>

    <div class="column alfie-column">
        <h2 class="column-title">Alfie Chou</h2>
        <ul class="course-list" id="alfieCourseList">
            <!-- Alfie的课程列表将通过JavaScript插入 -->
        </ul>
    </div>

    <div class="column jenny-column">
        <h2 class="column-title">Jenny Hsu</h2>
        <ul class="course-list" id="jennyCourseList">
            <!-- Jenny的课程列表将通过JavaScript插入 -->
        </ul>
    </div>

    <script>
        /* JavaScript代码将在下一部分提供 */
    </script>

</body>
</html>
<script>
const courseData = JSON.parse(localStorage.getItem('courseData')) || {
    alfie: {
        math: { level: 1, progress: 0 },
        linearAlgebra: { level: 1, progress: 0 },
        probability: { level: 1, progress: 0 },
        dataStructures: { level: 1, progress: 0 },
        computerOrganisation: { level: 1, progress: 0 },
        computerNetworks: { level: 1, progress: 0 },
        operatingSystems: { level: 1, progress: 0 },
        english: { level: 1, progress: 0 },
        politics: { level: 1, progress: 0 },
    },
    jenny: {
        civilLaw: { level: 1, progress: 0 },
        criminalLaw: { level: 1, progress: 0 },
        civilProcedureLaw: { level: 1, progress: 0 },
        criminalProcedureLaw: { level: 1, progress: 0 },
        administrativeLaw: { level: 1, progress: 0 },
        commercialKnowledge: { level: 1, progress: 0 },
        theoreticalLaw: { level: 1, progress: 0 },
        threeKingdomsLaw: { level: 1, progress: 0 },
    }
};

function updateProgress(person, course, change) {
    const personData = courseData[person];
    const courseInfo = personData[course];

    // Check if we need to decrease the level
    if (change < 0 && courseInfo.progress + change < 0) {
        if (courseInfo.level > 1) {
            courseInfo.level--;
            courseInfo.progress = 90;
        } else {
            courseInfo.progress = 0; // Cannot go below level 1
        }
    } else {
        courseInfo.progress += change;
        if (courseInfo.progress >= 100) {
            courseInfo.level++;
            courseInfo.progress = 0;
        }
    }

    // Ensure progress doesn't exceed 100%
    courseInfo.progress = Math.min(courseInfo.progress, 100);

    // Update UI
    document.getElementById(`${person}-${course}-level`).textContent = courseInfo.level;
    document.getElementById(`${person}-${course}-progress`).style.width = courseInfo.progress + '%';

    // Save to localStorage
    localStorage.setItem('courseData', JSON.stringify(courseData));
}
function createCourseItem(person, courseKey, courseInfo) {
    const courseNameMap = {
        // Alfie's and Jenny's courses
        math: '高等数学',
        linearAlgebra: '线性代数',
        probability: '概率论',
        dataStructures: '数据结构',
        computerOrganisation: '计算机组成原理',
        computerNetworks: '计算机网络',
        operatingSystems: '操作系统',
        english: '英语',
        politics: '政治',
        civilLaw: '民法',
        criminalLaw: '刑法',
        civilProcedureLaw: '民事诉讼法',
        criminalProcedureLaw: '刑事诉讼法',
        administrativeLaw: '行政法',
        commercialKnowledge: '商经知',
        theoreticalLaw: '理论法',
        threeKingdomsLaw: '三国法',
    };

    return `
        <li class="course-item" style="background-color: ${getCourseColor(courseKey)};">
            <h3>${courseNameMap[courseKey]}</h3>
            <div class="progress-container">
                <div class="progress" data-course="${courseKey}">
                    <div 
                        class="progress-bar" 
                        id="${person}-${courseKey}-progress" 
                        style="width: ${courseInfo.progress}%;">
                    </div>
                </div>
                <span class="course-level" id="${person}-${courseKey}-level">
                    ${courseInfo.level}
                </span>
            </div>
            <button onclick="updateProgress('${person}', '${courseKey}', -10)">-</button>
            <button onclick="updateProgress('${person}', '${courseKey}', 10)">+</button>
        </li>
    `;
}

function getCourseColor(courseKey) {
    // Define a map of course keys to color codes
    const courseColorMap = {
        math: '#e1f5fe',
        linearAlgebra: '#f3e5f5',
        probability: '#e8f5e9',
        dataStructures: '#fffde7',
        computerOrganisation: '#ffebee',
        computerNetworks: '#e0f7fa',
        operatingSystems: '#f9fbe7',
        english: '#fce4ec',
        politics: '#f3e5f5',
        civilLaw: '#ede7f6',
        criminalLaw: '#e8eaf6',
        civilProcedureLaw: '#e3f2fd',
        criminalProcedureLaw: '#ede7f6',
        administrativeLaw: '#fff3e0',
        commercialKnowledge: '#f1f8e9',
        theoreticalLaw: '#e8f5e9',
        threeKingdomsLaw: '#e0f2f1',
    };
    // Return the corresponding color or a default one if not found
    return courseColorMap[courseKey] || '#ffffff';
}

function renderCourseList() {
    const alfieCourseListElement = document.getElementById('alfieCourseList');
    const jennyCourseListElement = document.getElementById('jennyCourseList');

    alfieCourseListElement.innerHTML = Object.keys(courseData.alfie)
        .map(courseKey => createCourseItem('alfie', courseKey, courseData.alfie[courseKey]))
        .join('');

    jennyCourseListElement.innerHTML = Object.keys(courseData.jenny)
        .map(courseKey => createCourseItem('jenny', courseKey, courseData.jenny[courseKey]))
        .join('');
}

// Call renderCourseList to render the lists on load
renderCourseList();
</script>

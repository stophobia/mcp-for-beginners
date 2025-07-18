<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "748c61250d4a326206b72b28f6154615",
  "translation_date": "2025-07-02T09:40:19+00:00",
  "source_file": "05-AdvancedTopics/README.md",
  "language_code": "he"
}
-->
# נושאים מתקדמים ב-MCP

פרק זה מיועד לכסות סדרה של נושאים מתקדמים ביישום Model Context Protocol (MCP), כולל אינטגרציה רב-מודאלית, יכולת הרחבה, שיטות עבודה מומלצות לאבטחה ואינטגרציה ארגונית. נושאים אלה חיוניים לבניית יישומי MCP יציבים ומוכנים לפרודקשן שיכולים לעמוד בדרישות של מערכות AI מודרניות.

## סקירה כללית

שיעור זה חוקר מושגים מתקדמים ביישום Model Context Protocol, עם דגש על אינטגרציה רב-מודאלית, יכולת הרחבה, שיטות עבודה מומלצות לאבטחה ואינטגרציה ארגונית. נושאים אלו הכרחיים לבניית יישומי MCP ברמת פרודקשן שיכולים להתמודד עם דרישות מורכבות בסביבות ארגוניות.

## מטרות הלמידה

בסיום השיעור, תוכל:

- ליישם יכולות רב-מודאליות במסגרת MCP
- לעצב ארכיטקטורות MCP ניתנות להרחבה לתרחישים בעלי דרישה גבוהה
- ליישם שיטות עבודה מומלצות לאבטחה בהתאם לעקרונות האבטחה של MCP
- לשלב את MCP עם מערכות ומסגרות AI ארגוניות
- לאופטם ביצועים ואמינות בסביבות פרודקשן

## שיעורים ודוגמאות לפרויקטים

| קישור | כותרת | תיאור |
|------|-------|---------|
| [5.1 Integration with Azure](./mcp-integration/README.md) | אינטגרציה עם Azure | למד כיצד לשלב את שרת ה-MCP שלך ב-Azure |
| [5.2 Multi modal sample](./mcp-multi-modality/README.md) | דוגמאות רב-מודאליות ב-MCP | דוגמאות לתגובות קול, תמונה ורב-מודאליות |
| [5.3 MCP OAuth2 sample](../../../05-AdvancedTopics/mcp-oauth2-demo) | דמו OAuth2 ל-MCP | אפליקציית Spring Boot מינימלית המדגימה OAuth2 עם MCP, הן כשרת הרשאות והן כשרת משאבים. מציגה הנפקת טוקנים מאובטחת, נקודות קצה מוגנות, פריסת Azure Container Apps ואינטגרציה עם ניהול API. |
| [5.4 Root Contexts](./mcp-root-contexts/README.md) | הקשרים שורשיים | למד עוד על הקשר שורשי וכיצד ליישמו |
| [5.5 Routing](./mcp-routing/README.md) | ניתוב | למד סוגים שונים של ניתוב |
| [5.6 Sampling](./mcp-sampling/README.md) | דגימה | למד כיצד לעבוד עם דגימה |
| [5.7 Scaling](./mcp-scaling/README.md) | סקיילינג | למד על יכולת הרחבה |
| [5.8 Security](./mcp-security/README.md) | אבטחה | אבטח את שרת ה-MCP שלך |
| [5.9 Web Search sample](./web-search-mcp/README.md) | חיפוש אינטרנט ב-MCP | שרת ולקוח Python MCP המשולבים עם SerpAPI לחיפוש בזמן אמת באינטרנט, חדשות, מוצרים ושאלות ותשובות. מדגים תזמור כלים מרובים, אינטגרציה עם API חיצוני וטיפול שגיאות יציב. |
| [5.10 Realtime Streaming](./mcp-realtimestreaming/README.md) | סטרימינג בזמן אמת | סטרימינג של נתונים בזמן אמת הפך חיוני בעולם הנתונים של היום, בו עסקים ויישומים זקוקים לגישה מיידית למידע לקבלת החלטות בזמן אמת. |
| [5.11 Realtime Web Search](./mcp-realtimesearch/README.md) | חיפוש אינטרנט בזמן אמת | כיצד MCP משנה את חיפוש האינטרנט בזמן אמת על ידי מתן גישה סטנדרטית לניהול הקשר בין דגמי AI, מנועי חיפוש ויישומים. |
| [5.12  Entra ID Authentication for Model Context Protocol Servers](./mcp-security-entra/README.md) | אימות Entra ID | Microsoft Entra ID מספק פתרון ניהול זהויות וגישה מבוסס ענן חזק, המסייע להבטיח שרק משתמשים ואפליקציות מורשים יוכלו לתקשר עם שרת ה-MCP שלך. |
| [5.13 Azure AI Foundry Agent Integration](./mcp-foundry-agent-integration/README.md) | אינטגרציה עם Azure AI Foundry | למד כיצד לשלב שרתי Model Context Protocol עם סוכני Azure AI Foundry, המאפשרים תזמור כלים מתקדם ויכולות AI ארגוניות עם חיבורים סטנדרטיים למקורות נתונים חיצוניים. |

## הפניות נוספות

לקבלת המידע המעודכן ביותר על נושאים מתקדמים ב-MCP, עיין ב:
- [MCP Documentation](https://modelcontextprotocol.io/)
- [MCP Specification](https://spec.modelcontextprotocol.io/)
- [GitHub Repository](https://github.com/modelcontextprotocol)

## נקודות מרכזיות

- יישומי MCP רב-מודאליים מרחיבים את יכולות ה-AI מעבר לעיבוד טקסט
- יכולת הרחבה חיונית לפריסות ארגוניות וניתנת לטיפול באמצעות סקיילינג אופקי ואנכי
- אמצעי אבטחה מקיפים מגנים על הנתונים ומבטיחים בקרת גישה נכונה
- אינטגרציה ארגונית עם פלטפורמות כמו Azure OpenAI ו-Microsoft AI Foundry משפרת את יכולות ה-MCP
- יישומים מתקדמים של MCP נהנים מארכיטקטורות מותאמות וניהול משאבים קפדני

## תרגיל

עצב יישום MCP ברמת ארגון עבור מקרה שימוש ספציפי:

1. זיהוי דרישות רב-מודאליות למקרה השימוש שלך
2. תכנון אמצעי אבטחה להגנה על נתונים רגישים
3. עיצוב ארכיטקטורה ניתנת להרחבה שיכולה להתמודד עם עומסים משתנים
4. תכנון נקודות אינטגרציה עם מערכות AI ארגוניות
5. תיעוד צווארי בקבוק פוטנציאליים בביצועים ואסטרטגיות הפחתה

## משאבים נוספים

- [Azure OpenAI Documentation](https://learn.microsoft.com/en-us/azure/ai-services/openai/)
- [Microsoft AI Foundry Documentation](https://learn.microsoft.com/en-us/ai-services/)

---

## מה הלאה

- [5.1 MCP Integration](./mcp-integration/README.md)

**כתב ויתור**:  
מסמך זה תורגם באמצעות שירות תרגום מבוסס בינה מלאכותית [Co-op Translator](https://github.com/Azure/co-op-translator). למרות שאנו שואפים לדיוק, יש לקחת בחשבון שתרגומים אוטומטיים עלולים להכיל שגיאות או אי-דיוקים. המסמך המקורי בשפת המקור שלו הוא המקור הסמכותי. עבור מידע קריטי, מומלץ להשתמש בתרגום מקצועי אנושי. איננו אחראים לכל אי-הבנות או פרשנויות שגויות הנובעות משימוש בתרגום זה.
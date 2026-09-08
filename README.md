# Market Agent — סוכן מודיעין שוק יומי

Routine מתוזמן (Scheduled Routine) שרץ כל יום ב-08:00 UTC, סורק מקורות מידע פיננסיים/טכנולוגיים ושולח דו"ח יומי בעברית ל-Gmail.

## איפה מוגדר ה-Routine בפועל

ה-Routine מוגדר כ-Trigger בפלטפורמת Claude Code on the web (לא כקוד בריפו), `trig_01BmgCknJ74WxbnZv318M1qM`, שרץ כל יום ב-08:00 UTC (`0 8 * * *`). הפרומפט המלא, כולל כל דרישות המקורות/ניתוח/פורמט, מוטמע ב-Trigger עצמו.

**הערה טכנית**: ה-Trigger מוגדר לפעול על גבי אותו session שבו נוצר (mode "self-bind"), ולא כ-session חדש בכל הרצה. הסיבה: ליצירת session חדש בכל הרצה (`create_new_session_on_fire`) לא הייתה גישה אוטומטית ל-connector של Gmail בארגון הזה (`connectors parameter is not available for this organization`), ואילו ה-session הנוכחי כבר מחובר ל-Gmail. המשמעות: כל הרצה יומית ממשיכה את אותה שיחה (context מצטבר לאורך זמן, מה שגם מאפשר לסוכן להימנע מכפילויות בין דו"חות). אם Gmail יתנתק מה-session הזה בעתיד, יש ליצור מחדש את ה-Routine (מומלץ מה-UI של claude.ai/routines, שם ניתן לבחור connectors לכל trigger באופן מפורש).

## מקורות מידע

- **RSS חדשות**: Defense One (`defenseone.com/rss/all`), MIT Technology Review (`technologyreview.com/feed`), Ars Technica (`feeds.arstechnica.com/arstechnica/index`)
- **SEC EDGAR Full-Text Search**: `efts.sec.gov/LATEST/search-index` — חיפוש פילינגי 8-K (ציבורי, ללא צורך במפתח API)
- **GlobeNewswire / PR Newswire**: הודעות לפי קטגוריה (טכנולוגיה/AI, ביוטק, הגנה)

## הערת רשת חשובה

בבדיקה שבוצעה בסביבת הפיתוח, גישת `WebFetch` ישירה לדומיינים חיצוניים (כולל sec.gov, defenseone.com, prnewswire.com) **חסומה ע"י מדיניות ה-egress proxy** של הסביבה. כלי `WebSearch` (חיפוש מתארח, לא fetch ישיר) כן עובד. ה-Routine מוגדר להשתמש קודם כל ב-WebSearch, ולנסות WebFetch כגיבוי בלבד. אם מקור לא נגיש בזמן ריצה, הדו"ח יציין זאת בשקיפות במקום להמציא תוכן.
אם המדיניות תשתנה בעתיד (יתאפשר WebFetch), אפשר לעדכן את הפרומפט של ה-Trigger כדי לשלוף תוכן מלא מה-RSS/API ישירות.

## מיקוד

- שווי שוק: $100M–$2B
- סקטורים: AI ותשתית AI, ביוטק/פארמה, הגנה/ביטחון, אבטחת AI/סייבר לסוכנים אוטונומיים
- קטליזטורים: 8-K מהותי, אישור/דחיית FDA, חוזה ממשלתי/הגנתי, שותפות, הפתעת רווחים, פטנט/פריצת דרך

## פורמט ומסירה

דו"ח בעברית, מחולק לפי ידיעות, לכל ידיעה: תאריך אירוע בפועל, תאריך פרסום, קישור למקור, סימון עובדה/ספקולציה, דירוג אימפקט וכיוון. נשלח ל-Gmail בנושא "דו"ח כלכלי-טכנולוגי יומי - [תאריך]".

## עדכון ה-Routine

לעדכון תדירות/פרומפט/מקורות — יש לעדכן את ה-Trigger ישירות (לא קובץ בריפו קובע את ההתנהגות בפועל).

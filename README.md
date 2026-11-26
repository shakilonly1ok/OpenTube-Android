package com.opentube.ui.background

import androidx.compose.animation.core.LinearEasing
import androidx.compose.animation.core.animateFloat
import androidx.compose.animation.core.infiniteRepeatable
import androidx.compose.animation.core.rememberInfiniteTransition
import androidx.compose.animation.core.tween
import androidx.compose.foundation.Canvas
import androidx.compose.runtime.Composable
import androidx.compose.runtime.getValue
import androidx.compose.ui.Modifier
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.graphics.drawscope.DrawScope
import androidx.compose.ui.graphics.drawscope.drawIntoCanvas
import androidx.compose.ui.graphics.drawscope.scale
import androidx.compose.ui.graphics.drawscope.translate
import androidx.compose.ui.graphics.drawscope.withTransform
import androidx.compose.ui.graphics.Color.Companion.Black
import androidx.compose.ui.graphics.Color.Companion.Transparent
import androidx.compose.ui.geometry.Size
import androidx.compose.ui.graphics.drawscope.DrawScope.Companion.DefaultBlendMode
import androidx.compose.ui.unit.dp
import kotlin.math.PI
import kotlin.math.cos
import kotlin.math.sin

@Composable
fun ArtworkGlowBackground(
    colors: List<Color>,
    modifier: Modifier = Modifier,
    isPlaying: Boolean = true,
) {
    val transition = rememberInfiniteTransition(label = "glow")
    val animated by transition.animateFloat(
        initialValue = 0f,
        targetValue = (2 * PI).toFloat(),
        animationSpec = infiniteRepeatable(
            animation = tween(durationMillis = 14000, easing = LinearEasing),
        ),
        label = "phase",
    )
    val phase = if (isPlaying) animated else 0f

    Canvas(modifier) {
        drawRect(Color(0xFF0B0B0D))

        colors.take(4).forEachIndexed { i, color ->
            val speed = 0.6f + i * 0.25f
            val cx = size.width * (0.5f + 0.35f * cos(phase * speed + i))
            val cy = size.height * (0.35f + 0.25f * sin(phase * speed * 0.8f + i * 1.3f))
            val radius = size.maxDimension * 0.7f
            drawCircle(
                brush = Brush.radialGradient(
                    colors = listOf(color.copy(alpha = 0.7f), color.copy(alpha = 0f)),
                    center = Offset(cx, cy),
                    radius = radius,
                ),
                radius = radius,
                center = Offset(cx, cy),
            )
        }

        drawRect(
            brush = Brush.verticalGradient(
                colors = listOf(Transparent, Black.copy(alpha = 0.55f)),
            ),
        )
    }
}
